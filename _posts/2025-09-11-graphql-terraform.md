---
layout: post
title: Extending Terraform with GraphQL for managing GitLab resources
date: 2025-09-11 07:00:01 +0100
categories: terraform gitlab graphql
---

Hello, everyone! In this post I'd like to share a small example of how to combine **Terraform** with **GraphQL** to manage **GitLab** resources that aren't supported by the official Terraform provider (yet).

## The problem

The official [GitLab Terraform provider](https://registry.terraform.io/providers/gitlabhq/gitlab/latest) is powerful and covers a lot of functionality. But like any provider, it doesn't expose *every* (especially new) GitLab feature.
At the same time, almost everything in GitLab can be managed through the [GraphQL API](https://docs.gitlab.com/api/graphql/). So if Terraform doesn't support something you need, GraphQL is often the way forward.
That's exactly what I wanted to show here, in this post. I recently had to deal with similar problem and I hope this post will be usefull for someone out there.

## What GraphQL?

GraphQL is a query language and runtime for APIs. Unlike REST, it lets the client request only the data it needs, and nothing more. It's flexible, efficient, and in GitLab's case it covers features that aren't available in the Terraform provider.
For this example, I'm using the [`sullivtr/graphql`](https://registry.terraform.io/providers/sullivtr/graphql/latest) provider, which makes it easy to call GraphQL from Terraform.  

To demonstrate this I created a small example project - [gitlab-graphql-terraform-example](https://github.com/dunterov/gitlab-graphql-terrafrom-example).

## What's in this demo project

The example project shows how to use Terraform with GraphQL to manage [GitLab Dependency proxy for packages settings](https://docs.gitlab.com/user/packages/package_registry/dependency_proxy/).  

Here's what it does:  

* **Enables or disables** the Dependency Proxy for a project.
* **Configures an external Maven registry URL** through GraphQL.
* Uses the same GraphQL mutation for both **create** and **update** actions.
* On `terraform destroy`, the resource runs a mutation that disables the Dependency Proxy (because we only can enable it or disable. In the logic of the example destroy == disable).

So essentially, this project shows how to:  

1. Write a GraphQL mutation in Terraform.
2. Pass variables into it (like `enabled` or `mavenExternalRegistryUrl`).
3. Handle `create`, `update`, `delete`, and `read` operations.

It's a small, focused example, but the same pattern can be applied to any GitLab feature that's available in GraphQL but not yet exposed in the Terraform provider.  

## If you want to try

If you like the idea and want to try this, follow the steps:

1. Create [Personal access token](https://docs.gitlab.com/user/profile/personal_access_tokens/) for your GitLab user.
2. Configure required variables (via `terraform.tfvars` file or CLI):

   ```hcl
   gitlab_base_url = "https://gitlab.com"
   gitlab_token    = "<your-token>"
   full_path       = "group/project"
   ```

    where:
    
     - `gitlab_base_url` is a url of your Gitlab instance.
     - `gitlab_token` is a token from **step 1**.
     - `full_path` is a path to the project as it shown in URL.

3. Initialize Terraform:

   ```sh
   terraform init
   ```

4. Apply changes:

   ```sh
   terraform apply
   ```

## Final thoughts  

This is just a small example, but it shows a useful pattern: if the Terraform provider doesn't support what you need, you don't have to wait. You can drop down to **GraphQL** and still keep everything under Terraform's management.  

Hope this helps someone extend their Terraform usage!  
See you soon!
