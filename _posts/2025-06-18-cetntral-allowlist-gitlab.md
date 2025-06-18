---
layout: post
title: Simple idea for centralizing the control of the vulnerability-allowlist.yml configuration file
date: 2025-06-18 07:00:01 +0100
categories: gitlab ci security
---

In this post, I'll share a specific situation I had some time ago and how I went about solving it.
It's a rather niche case, but it may be of interest to those who deal with similar challenges and:
- use GitLab
- use `Container-Scanning.gitlab-ci.yml` [GitLab template](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Jobs/Container-Scanning.gitlab-ci.yml)
- want to manage `vulnerability-allowlist.yml` from a single location
- ...or simply curious what it's all about

### The setup

If you have ever worked with a large GitLab instance, you probably know how managing even simple tasks can become challenging over time. What should be simple enough task may turn into repetitive copy-paste work. I'm no exception. Eventually, I found myself managing groups and projects in GitLab that looked something like this:

```
.
└──team-group
    ├── project-with-dockerfiles
    ├── project-with-ci-templates
    ├── project0
    ├── project1
    ├── project2
    └── ... so on ...
```

Where:

- `team-group` is the top-level GitLab group for my team.
- `project-with-dockerfiles` is a repository containing all our Dockerfiles. It's responsible for building container images and pushing them to the GitLab container registry.
- `project-with-ci-templates` holds all our custom GitLab CI job templates.
- `project0`, `project1`, `project2`, etc. are application repositories that use the images from `project-with-dockerfiles` via `FROM ...` statements in Dockerfiles, and reuse CI job templates from `project-with-ci-templates` in their `.gitlab-ci.yml` files.
- `... and so on ...` indicates there are many more projects following this same structure.

A shared pipeline is defined in the `project-with-ci-templates` repository and included in individual projects using the following configuration (with minor variations across the projects):

```yaml
include:
  - project: "team-group/project-with-ci-templates"
    file: "/common-pipeline.yml"
    ref: "v1.2.3"
```

The `common-pipeline.yml` file includes the [Container-Scanning.gitlab-ci.yml](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Jobs/Container-Scanning.gitlab-ci.yml) template, which is part of the GitLab [Auto-DevOps](https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Auto-DevOps.gitlab-ci.yml) bundle.

As a result, all projects automatically incorporate the `container_scanning` job as part of their pipeline.
Each project generates its own Vulnerability Report based on the scan results.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/gitlab_vuln_report.png" alt="Vulnerability report tab" title="Vulnerability report tab" width="300">
</div>

At the group level, GitLab aggregates vulnerability reports from all projects into a single, compiled view.
Starting to see the issue?

### The problem

Yes, that's right. The compiled report at the top-level group includes vulnerabilities from *all* projects. And that's perfectly fine. However, there's a catch.

All projects use base images built in the `project-with-dockerfiles` repository. So, if a vulnerability exists in one of those base images, it will naturally appear in any downstream image built from it and thus show up in every project's report.

This *could be* acceptable. But in one case, I needed to exclude a well-known vulnerability that the team had reviewed and agreed posed no real risk. Leaving it in there would just create unnecessary noise.
GitLab does allow such exclusions by adding known vulnerabilities to a [special file](https://docs.gitlab.com/user/application_security/container_scanning/#vulnerability-allowlisting) called `vulnerability-allowlist.yml`, which must be placed in the root of each project.

Now imagine having to do that manually across tens of projects… Living a dream! That's why I came up with

### The solution

As mentioned earlier, the `vulnerability-allowlist.yml` file must be placed in the root directory of each project. In my case, all projects share the same set of base images, so one possible approach was to add the following configuration (see the required data format [here](https://docs.gitlab.com/user/application_security/container_scanning/#vulnerability-allowlistyml-data-format)) to every project.

```yaml
generalallowlist:
  CVE-XXXX-YY1: package
  CVE-XXXX-YY2:
  CVE-XXXX-YY3: 
```

But obviously, the next time a similar case arises, I'd need to open merge requests in tens of repositories - far from an ideal solution.

But wait! All projects already use CI templates from `team-group/project-with-ci-templates`! So, I took advantage of that and implemented a simple solution. I added a `before_script` step to the `container_scanning` job in my `common-pipeline.yml`(simplified slightly here for clarity). The logic is simple: **if the project doesn't have its own `vulnerability-allowlist.yml` in the root, fall back to the one from `project-with-dockerfiles`**.

```yaml
container_scanning:
  before_script: |
    checkout_dir=$(mktemp -d /tmp/checkoutXXXXXX)
    if [ ! -e "vulnerability-allowlist.yml" ]; then
      git clone https://my.gitlab.instance/team-group/project-with-dockerfiles.git $checkout_dir
      cp $checkout_dir/vulnerability-allowlist.yml .
      echo "The job will use 'vulnerability-allowlist.yml' file from a 'project-with-dockerfiles' repository."
    else
      echo "The original 'vulnerability-allowlist.yml' file will be used for this job."
    fi
```

The image used by the `container_scanning` job by default is quite minimal - for example, it doesn't include tools like curl for downloading files. However, since git is available, I just used `git clone` instead.

And that's it! Now, adding a well-known vulnerability to the allowlist can be done in just one place. The time saved can be better spent on more meaningful work.

And that's all for now. Have a great day!
