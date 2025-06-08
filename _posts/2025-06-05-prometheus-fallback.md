---
layout: post
title: Trick to make Prometheus ignore wrong content-type on metrics endpoint
date: 2025-06-09 10:00:01 +0100
categories: prometheus monitoring
---

One day, I had to integrate a third-party metrics exporter into our monitoring stack (a pretty standard setup with Prometheus, Alertmanager, and Grafana). The exporter exposed a metrics endpoint that, when opened in a browser, simply displayed a list of metrics. However, when I added it to the Prometheus config as a new target, Prometheus broke with the `err: received unsupported Content-Type "application/json"`. In this post, I'll explain why that happened and how I fixed it. Hopefully, this will be useful to someone out there.

### What happened?

First of all, a few words about metrics endpoints. I'm not going to share the original one, but it looked something like this:

```
https://their.host.com/api/metrics?company=mycorp
```

As usual, I easily converted it into a standard Prometheus scrape configuration:

```yaml
- job_name: 'their_metrics'
    scrape_interval: 1m
    metrics_path: /api/metrics
    scheme: https
    static_configs:
      - targets: ['their.host.com']
    params:
      company: ['mycorp']
```

But surprise, surprise! After restarting Prometheus, I saw this in the logs:

```
{"time":"XXXX-XX-XXT18:00:49.102945354Z","level":"ERROR","source":"scrape.go:1609","msg":"Failed to determine correct type of scrape target.","component":"scrape manager","scrape_pool":"their_metrics","target":{},"content_type":"application/json","fallback_media_type":"","err":"received unsupported Content-Type \"application/json\" and no fallback_scrape_protocol specified for target"}
```

### Why?

Long story short - **wrong "content-type"**!

As I mentioned, the page looked fine in a browser - just the usual list of metrics. But when I opened it with curl, something interesting was revealed:

```shell
curl -vvv https://their.host.com/api/metrics?company=mycorp
 
 < HTTP/1.1 200 OK
 < content-type: application/json
 < content-length: 817
 < date: Wed, XX XXX XXXX 18:15:44 GMT
 <
```

What? `content-type: application/json`? On a page that is just a plain text?

### What to do with that?

Well, In a perfect world I'd fix the exporter and make it return the correct "content-type". But this was an urgent task and we had no time to address the issue properly.

So I had to tune the config on our side. Luckily, Prometheus scrape config has a key `fallback_scrape_protocol`. From [docs](https://prometheus.io/docs/prometheus/latest/configuration/configuration/):

```
# Fallback protocol to use if a scrape returns blank, unparseable, or otherwise
# invalid Content-Type.
# Supported values (case sensitive): PrometheusProto, OpenMetricsText0.0.1,
# OpenMetricsText1.0.0, PrometheusText0.0.4, PrometheusText1.0.0.
```

So what I did is I added this to the scrape config

```yaml
- job_name: 'their_metrics'
    scrape_interval: 1m
    metrics_path: /api/metrics
    scheme: https
    static_configs:
      - targets: ['their.host.com']
    params:
      company: ['mycorp']
  fallback_scrape_protocol: PrometheusText0.0.4
```

and the issue went away. It's not perfect in any sense, but this is **how we can tell Prometheus what to use if it can't parse the page content**.

Hope this helps someone out there and save a few minutes of troubleshooting.

Take care!
