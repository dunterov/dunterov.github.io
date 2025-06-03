---
layout: post
title: Using Terraform's write-Only arguments with GCP Secret Manager
date: 2025-06-03 10:00:01 +0100
categories: terraform gcp
---

Starting with Terraform [v1.11](https://www.hashicorp.com/en/blog/terraform-1-11-ephemeral-values-managed-resources-write-only-arguments), we now have a cleaner and more secure way to handle sensitive values — **write-only** arguments. This post walks through a simple example of using that feature with Google Cloud Secret Manager.

### What's the Problem?

Before this feature, if you wanted to create a secret version in GCP, your secret value had to go directly in your Terraform code or variable, and unfortunately it ended up stored in plain text in your state file (or plan file).

Example:

```tf
resource "google_secret_manager_secret_version" "version" {
  secret      = "some-secret-id"
  secret_data = "my secret"
}
```

That "my secret" string? It'd sit right there in your state. Not ideal.

### Write-Only Arguments

Terraform 1.11+ introduces write-only fields — like `secret_data_wo` and `secret_data_wo_version`. These let you safely pass secrets at runtime without saving them in your state file.

Here's how the new approach looks:

```tf
variable "secret_string" {
  type    = string
  default = "my-secret"
}

resource "google_secret_manager_secret_version" "version" {
  secret                 = "some-secret-id"
  secret_data_wo         = var.secret_string
  secret_data_wo_version = parseint(substr(sha256(var.secret_string), 0, 4), 16)
}
```

In this example:

- secret_data_wo: passed at runtime, not saved in state
- secret_data_wo_version: stored in state, used to track changes

You're still triggering updates when the secret changes, but the secret itself never touches the state file. Nice.

### But Wait! What's That Hash Expression?

This line is doing some lightweight versioning:

```tf
parseint(substr(sha256(var.secret_string), 0, 4), 16)
```

**Breakdown**:

- Hashes the secret string with SHA-256
- Takes the first 4 hex chars (16 bits)
- Parses it as an integer

It's not cryptographically bulletproof, but it's good enough for:

- Triggering secret updates when content changes
- Avoiding full-hash complexity
- Preventing secrets from leaking into state

This approach is a pragmatic middle ground — especially when your secrets are already high-entropy, like API keys or JWTs.

### Final Tip

If you're using a file to pass in secrets (`terraform.tfvars` or similar), make sure to add it to `.gitignore` so you don't accidentally commit your secrets.

**TL;DR**

- Use *_wo fields (for supported resources) to avoid secrets leaking into state
- Use a short hash-based version number to track changes
- Works great for high-entropy secrets and keeps your infra code clean and safe

The example code is available in [this my repository](https://github.com/dunterov/write-only-gcp-terrafrom-example).

Bye-bye!
