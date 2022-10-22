alertmanager-discord
===

Give this a webhook (with the DISCORD_WEBHOOK environment variable) and point it as a webhook on alertmanager, and it will post your alerts into a discord channel for you as they trigger:

![](/.github/demo-new.png)

## Warning

This program is not a replacement to alertmanager, it accepts webhooks from alertmanager, not prometheus.

The standard "dataflow" should be:

```
Prometheus -------------> alertmanager -------------------> alertmanager-discord

alerting:                 receivers:                         
  alertmanagers:          - name: 'discord_webhook'         environment:
  - static_configs:         webhook_configs:                   - DISCORD_WEBHOOK=https://discordapp.com/api/we...
    - targets:              - url: 'http://localhost:9094'  
       - 127.0.0.1:9093   





```

## Example alertmanager config:

```
global:
  # The smarthost and SMTP sender used for mail notifications.
  smtp_smarthost: 'localhost:25'
  smtp_from: 'alertmanager@example.org'
  smtp_auth_username: 'alertmanager'
  smtp_auth_password: 'password'

# The directory from which notification templates are read.
templates: 
- '/etc/alertmanager/template/*.tmpl'

# The root route on which each incoming alert enters.
route:
  group_by: ['alertname']
  group_wait: 20s
  group_interval: 5m
  repeat_interval: 3h 
  receiver: discord_webhook

receivers:
- name: 'discord_webhook'
  webhook_configs:
  - url: 'http://localhost:9094'
```

## CRI-O

The repo contains a helm chart accessing the container image at https://quay.io/klenkes74/alertmanager-discord.
The only parameter to be set is `discord.webhook` which contains the configured web hook for your discord channel.
For the default installation you may run a single alertmanager-discord or you seperate the channels by your own criteria (normaly severity) by running the helm chart multiple times for every single channel you want to fill with alerts.

## Thanks

Thanks to benjojo at github.com for the alert manager discord webhook itself. I only added the helm chart and a little change to the repo structure. The original can be found on https://github.com/benjojo/alertmanager-discord.