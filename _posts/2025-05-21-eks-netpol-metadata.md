---
layout: post
title: Blocking pod access to metadata in EKS
date: 2025-05-19 10:00:01 +0100
categories: aws eks
---

Some time ago, I got a task from our security team: ensure that pods running in our EKS cluster couldn't access the instance metadata endpoint. This was part of a broader effort to tighten security and prevent potential credential exposure. In this post, I'll walk you through how I did this with a combination of two Kubernetes egress NetworkPolicies.

### What is a Kubernetes Network Policies?

A [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) is a way to control the network traffic flow at the IP address or port level between pods, and between pods and other network endpoints.

It allows us, k8s users, to define rules that specify what kind of traffic is allowed to or from a group of pods — effectively acting like a firewall inside your Kubernetes cluster (with some limitations but this post is not about that).

Key moving parts are:

- PodSelector: Defines which pods the policy applies to.
- Ingress rules: Control incoming traffic to the selected pods.
- Egress rules: Control outgoing traffic from the selected pods.
- PolicyTypes: Specify whether the policy is for Ingress, Egress, or both.
- IPBlock / NamespaceSelector / PodSelector: Allow traffic based on IPs or other pods/namespaces.

For the task this post is about I used "Egress rules". By the way, this functionality might be disabled in your EKS cluster, please check your setup before trying what is described in this post. More information available in [AWS Docs](https://docs.aws.amazon.com/eks/latest/userguide/cni-network-policy.html).

### Konfiguring policies to block a pod access to instance metadata in EKS

So, this is a bit tricky, but we need two policies.

- One to block all egress traffic
- Another one to allow all traffic except `169.254.169.254/32`

To do this, create file called `np.yaml` with the content shown below:

```yaml
---
 apiVersion: networking.k8s.io/v1
 kind: NetworkPolicy
 metadata:
   name: deny-egress-all
 spec:
   podSelector: {}
   policyTypes:
   - Egress
 ---
 apiVersion: networking.k8s.io/v1
 kind: NetworkPolicy
 metadata:
   name: allow-egress-all-except-metadata
 spec:
   podSelector: {}
   policyTypes:
   - Egress
   egress:
   - to:
     - ipBlock:
         cidr: 0.0.0.0/0
         except:
         - 169.254.169.254/32
```

The combined effect will do the trick! Now, apply this to the namespace you need:

```bash
kubectl apply -f np.yaml -n <your namespace here>
```

And that's it! To verify policies are applied you can check they are actually there with `kubectl get networkpolicy -n <your namespace here>` or `kubectl describe networkpolicy deny-egress-all -n <your namespace here>`.
If all applied successfully, you can run interactive shell from inside the pod and try to access the metadata.
For this you can do something like this. First, prepare environment for the test

```bash
kubectl run -it ubuntu-temp --image=ubuntu --restart=Never -- bash
apt update
apt install curl
```

And then, try to `curl` to and endpoint of `http://169.254.169.254/latest/meta-data/`. If you did everything correct the network policies will prevent pod from accessing to metadata.

I guess that's it for now!