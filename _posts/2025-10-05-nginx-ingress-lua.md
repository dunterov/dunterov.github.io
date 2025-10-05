---
layout: post
title: Blocking requests at the edge with NGINX Ingress and Lua
date: 2025-10-05 07:00:01 +0100
categories: kubernetes nginx lua
---

Hello, Internet! In this post, I'll show how to use Lua scripting within the NGINX Ingress Controller to implement advanced traffic filtering and request handling logic. It's an approach I used to solve a problem some time ago. In short, the post is about how Lua can help apply fine-grained request filtering right at the edge (in ingress definitions via annotations). If that sounds interesting, this post is for you.

## Background

Sometimes, you don't want certain traffic to even reach your application pods. This could include bots hitting known paths, legacy clients using outdated User-Agents, unsafe or unexpected HTTP methods, or requests coming from spammy or untrusted Referers.

While you could handle these in your app code or API gateway, it's also possible to do it directly in NGINX Ingress - though in most cases, a **proper WAF would be the recommended solution**. I put together a small repository of examples showing how to use Lua scripting inside the NGINX Ingress Controller to implement this kind of logic.

[GitHub Repository - dunterov/nginx-k8s-ingress-lua-example](https://github.com/dunterov/nginx-k8s-ingress-lua-example)

## Why Lua?

The NGINX Ingress Controller already provides several annotations for basic request control, as to whitelist IPs or block specific methods. But once you need **more complex or conditional logic**, such as combining method and URI filters, matching regex patterns, or inspecting multiple headers together, the built-in annotation syntax can quickly becomes limiting.

With the built-in NGINX annotation syntax it is possible to do simple checks, but building complex rules that combine multiple conditions, perform regex matching, or decode and inspect values across headers and URIs becomes awkward or impossible without Lua. Lua scripting gives you the missing expressiveness and lets you write compact, maintainable logic that runs in the request lifecycle.

Lua is built into NGINX and is available in the Ingress Controller. It allows you to execute small logic blocks during request phases such as `access_by_lua_block`, so you can implement policies like:

```lua
if method == "DELETE" then
  return ngx.exit(ngx.HTTP_FORBIDDEN)
end
```

... without rebuilding or redeploying the controller.

## What's in the repository

The repository provides several Ingress manifest examples demonstrating how to block or filter requests based on different request attributes such as:

- User-Agent headers  
- Request URIs  
- HTTP methods  
- Referer values  
- Or combinations of these together  

Each example uses a simple `access_by_lua_block` within the Ingress annotation to perform request-time checks and return `403 Forbidden` responses when conditions are met. They are minimal, composable, and easy to adapt for your own policies.

## Prerequisites

To run these examples, make sure your NGINX Ingress Controller is configured to allow Lua snippets:

1. Install the official NGINX Ingress Controller - https://github.com/kubernetes/ingress-nginx

2. Enable snippet annotations (Helm option):  

   ```bash
   helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
      --set controller.allowSnippetAnnotations=true
   ```

3. Allow Lua directives in your ConfigMap:  

   Make sure `annotation-value-word-blocklist` does **not** contain `_by_lua` directives. Example allowed config snippet:

   ```yaml
   data:
     annotation-value-word-blocklist: "load_module,lua_package"
   ```

Also review the ingress-nginx README and your controller configuration to ensure any organization security policies or admission controllers permit snippet annotations in your environment.

# Quick example

Here's a minimal example that blocks `DELETE` requests:

```yaml
nginx.ingress.kubernetes.io/configuration-snippet: |
  access_by_lua_block {
    local method = ngx.req.get_method()
    if method == "DELETE" then
      return ngx.exit(ngx.HTTP_FORBIDDEN)
    end
  }
```

Apply and test it:

```bash
kubectl apply -f example-ingress-block-method.yaml
curl -X DELETE https://example.com/
# You'll get 403 Forbidden
```

Check repository to see all examples. 

And that's it for today! While this is a working method, I still recommend using proper, production-ready solutions whenever possible. That said, in some cases, this approach can be very useful. See you next time!
