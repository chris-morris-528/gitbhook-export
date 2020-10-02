---
description: Develop powerful integrations to better serve the workplace
---

# Envoy Integrations Platform

## What's an Integration?

Integrations are either [internal plugins](plugins/) installable as part of the Envoy dashboard, or [external applications](external-apps.md) embedded into a 3rd party system. Both integration types provide authenticated access to Envoy's API.

## Plugins vs. External Apps

Both integration types provide their own set of benefits and tradeoffs, as outlined below:

### Plugins

* Envoy customers discover and setup the integration directly in the Envoy dashboard. Our Integration Builder allows you to quickly and easily describe your setup steps and the required form fields. 
* Plugins may register webhooks to listen for events that happen within Envoy.
* There are a few rules that must be followed in order to ensure proper setup step and webhook handling by your plugin.

### External Apps

* Envoy provides a link out from our Integrations page to your application.
* You must handle the proper setup and information gathering from the Envoy user.
* The only requirement to use the Envoy API is for an Envoy user to participate in the OAuth2 flow, which grants your application the requested permissions.

Since external apps are more free-form, most of our documentation will be dedicated to plugins.



