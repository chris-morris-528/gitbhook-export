---
description: '"Deep" integrations that look and function in line with the rest of Envoy.'
---

# Plugins

## Key Concepts

* **Job** - An instance of an event that's sent to a plugin. E.g. when an "entry sign in" event is fired, each plugin listening for that event has a "job" created for it. This entity allows us to attach metadata to that event on a plugin-by-plugin basis. Jobs have a status, message, and attachments \(optional\), all of which can be updated by the plugin.
* **Worker** - a plugin endpoint, used to respond to a Job. They are the Job's event handlers.
* **Route** - a plugin endpoint, used to send or receive data during setup. These endpoints are proxied though Envoy.
* **Release** - each time a plugin is updated, a release is created. Each release encompasses the current definition of that plugin.
* **Installation** - an entity representing the installation of a plugin at a specific company or location. This entity is useful for storing and isolating plugin config data as well as plugin storage.
* **Config** - during installation, customers are typically asked to input data or login to some external system. This data is stored in the plugin config, and is made available to the plugin on each invocation \(either a route or worker invocation\).
* **Storage** - a key/value storage that plugins can use to save data. This storage is not visible anywhere except to plugins, which makes it a good choice for sensitive data like passwords. While it is mostly used by workers to store data, routes may opt to use it to store passwords instead of saving them in the config, which is visible to the Envoy dashboard.
* **Attachment** - useful data that can be tied to a job. They show up on the right-hand sidebar in the Envoy dashboard when viewing a visitor or invite. E.g. wi-fi plugins often attach the generated password to "entry sign in" jobs so that they are visible to administrators.

