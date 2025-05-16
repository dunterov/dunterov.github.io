---
layout: post
title: Monitor Pantheon Site Usage with Terminus Env Metrics Exporter and Prometheus
date: 2025-05-16 10:00:01 +0100
categories: monitoring prometheus
---

Hi Internet! I recently built a small utility to scratch my own itch, and I thought it might be useful to others too.

Meet [Terminus Env Metrics Exporter](https://github.com/dunterov/terminus-env-metrics-exporter) — a lightweight, open-source Prometheus exporter that pulls environment-level metrics from Pantheon sites using Terminus and makes them available for monitoring. Give it a look!

### What Is It?

Terminus Env Metrics Exporter is a tiny Python tool that periodically runs the `terminus env:metrics` command against your Pantheon sites, parses the output, and exposes the data as Prometheus-compatible metrics.

So this:

```bash
terminus env:metrics my-pantheon-site.live --format=json --period=day --datapoints=1
```

becomes this:

```
# HELP terminus_env_visits Number of visits
# TYPE terminus_env_visits gauge
terminus_env_visits{env="live",site="my-pantheon-site"} 10
# HELP terminus_env_pages_served Pages served
# TYPE terminus_env_pages_served gauge
terminus_env_pages_served{env="live",site="my-pantheon-site"} 20
# HELP terminus_env_cache_hits Cache hits
# TYPE terminus_env_cache_hits gauge
terminus_env_cache_hits{env="live",site="my-pantheon-site"} 15
# HELP terminus_env_cache_misses Cache misses
# TYPE terminus_env_cache_misses gauge
terminus_env_cache_misses{env="live",site="my-pantheon-site"} 5
# HELP terminus_env_cache_hit_ratio Cache hit ratio (percent)
# TYPE terminus_env_cache_hit_ratio gauge
terminus_env_cache_hit_ratio{env="live",site="my-pantheon-site"} 75.00
```

...so Prometheus can scratch it once added as a target.

### Why Use It?

Pantheon provides powerful hosting, but the visibility into environment-level metrics (visits, pages served, cache hits/misses, etc.) can be limited if you're not logged into their dashboard.

With my tiny Terminus Env Metrics Exporter, you can:

- Bring Pantheon traffic metrics into your Prometheus dashboards
- Set up alerts for sudden drops or spikes in traffic or cache efficiency
- Track performance trends over time
- Combine Pantheon metrics with other infrastructure data for a unified observability setup

### Use cases

Using data provided, you can 

- Have daily traffic trend dashboards per site and environment
- Add cache efficiency panels to monitor hit/miss ratios
- Configurr alerts for traffic drops indicating possible downtime


This tool is open source and community-friendly! If you have suggestions, find bugs, or want to contribute improvements, head over to the GitHub repo and get involved.
