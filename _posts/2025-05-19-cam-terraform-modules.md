---
layout: post
title: Collection of useful Terraform modules for GCP
date: 2025-05-19 10:00:01 +0100
categories: iac terraform
---

Hi everyone! If you, like me, work a lot with Terraform (and Infrastructure as Code in general) and GCP, you'll probably find this collection of publicly available modules quite useful. I'm proud to say that I've contributed to many of them.
In this post, you'll find a collection of links to the modules developed at the **University of Cambridge** along with a brief description for each module.

### GCP Cloud Run app

**Link**: https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform/gcp-cloud-run-app

**What is this**: This module manages the deployment of containerised applications on Cloud Run.

**Example** (check the project for more):

```
module "webapp" {
  source  = "gitlab.developers.cam.ac.uk/uis/gcp-cloud-run-app/devops"
  version = "~> 9.0"

  region  = "europe-west2"
  project = "example-project-id-1234"

  containers = {
    webapp = {
      image = "us-docker.pkg.dev/cloudrun/container/hello"
    }
  }
}
```

### GCP Minimal Site Monitoring

**Link**: https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform/gcp-site-monitoring

**What is this**: This module provisions basic monitoring for a website.

**Example** (check the project for more):

```
module "monitoring" {
  source  = "gitlab.developers.cam.ac.uk/uis/gcp-site-monitoring/devops"
  version = "~> 4.0"

  host                        = "www.example.com"
  alert_notification_channels = ["projects/[PROJECT_ID]/notificationChannels/[CHANNEL_ID]"]
}
```

### Google Cloud Secrets Manager

**Link**: https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform/gcp-secret-manager

**What is this**: This module will create a Google Secret Manager secret and associated secret version with a passed
content.

**Example** (check the project for more):

```
module "secret" {
  source  = "gitlab.developers.cam.ac.uk/uis/gcp-secret-manager/devops"
  version = "~> 5.0"

  project   = "some-project-id"
  region    = "europe-west2"
  secret_id = "test-secret"
}

```

And there's more! Worth to mention [GKE Cluster](https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform/gke-cluster) and [GCP Scheduled Script](https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform/gcp-scheduled-script) at the very least. 

The full list you can find [here](https://gitlab.developers.cam.ac.uk/uis/devops/infra/terraform).
I use these modules actively in my daily work, and they continue to improve over time. I hope you'll find them useful as well.

That's all for now. Take care!
