---
layout: post
title: How to send alerts from Alertmanager to MS Teams
date: 2025-07-27 07:00:01 +0100
categories: teams alertmanager
---

Some time ago, at a company I was working with, we kicked off a new project where the team was using Microsoft Teams for communication. One of the first tasks was to get Alertmanager to send alerts directly into a Teams channel using so-called Workflows.<br>
I searched around but couldn't find a clear, step-by-step guide for a quick start. So I decided to write one.
In this short post, I'll walk you through how to quickly set up Alertmanager to send alerts to a Microsoft Teams channel.

## Teams configuration

First, create a channel where Alertmanager can send its alerts. To do this, open the desired team in Microsoft Teams and click "Add channel".

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/am_teams_1.png" alt="Teams integration" title="Teams integration" width="200">
</div>

Next, enter a channel name and make sure to set the channel type to Standard. This is important because the workflow used for alert forwarding [does not support Private channels](https://learn.microsoft.com/en-us/power-automate/teams/send-a-message-in-teams#known-issues-and-limitations).

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/am_teams_2.png" alt="Teams integration" title="Teams integration" width="80%">
</div>

Now, open the newly created channel and select "Workflows" from the menu.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/am_teams_3.png" alt="Teams integration" title="Teams integration" width="200">
</div>

In the dialog, choose the option "Post to a channel when a webhook request is received".

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/am_teams_4.png" alt="Teams integration" title="Teams integration" width="80%">
</div>

In the next dialog, provide a name for the workflow and select a connection.

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/am_teams_5.png" alt="Teams integration" title="Teams integration" width="80%">
</div>

Click "Next" and confirm the channel. Once the setup is complete, Microsoft Teams will display a webhook URL. Make sure to save it, as it will be needed later.

That completes the Microsoft Teams setup.

## Alertmanager configuration

On the Alertmanager side, the `msteamsv2_configs` [configuration](https://prometheus.io/docs/alerting/latest/configuration/#msteamsv2_config) block is used. For a basic setup (like the one in this guide with a single receiver and default route) the Alertmanager YAML configuration file might look like the example below:

```yaml
global:
  resolve_timeout: 1m

route:
  receiver: 'teams'
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 3h

receivers:
  - name: 'teams'
    msteamsv2_configs:
    - webhook_url: https://your_webhook_here
      send_resolved: true
```

*(Tip: There are many different ways to set up and configure Alertmanager. However, it's generally not a good idea to store secrets directly in configuration files. At a minimum, especially for on-premises setups, use the `webhook_url_file` option to keep sensitive data secure.)*

Thats basically it! Of course, there are many ways to fine-tune the setup. For example, the default alert message layout isn't the most polished I've seen. But this short article provides a good starting point and a base for further improvements. Hope you found this helpful!

See you all next time!
