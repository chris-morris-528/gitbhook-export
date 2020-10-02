---
description: >-
  When Envoy makes requests to your plugin's endpoints, we send along some
  metadata in the request body.
---

# Request Meta

The `meta` object is found in the body of every request Envoy makes to your plugin. It contains data that is often critical to properly handling the request.

In the [Node.js SDK](../the-node.js-sdk/), you can access the `meta` object via `req.envoy.meta.` 

## If the Request is to a Route

**Properties**

| Name | Type | Description |
| :--- | :--- | :--- |
| route | `string` | the name of the route |
| plugin\_id | `number` | the plugin ID |
| install\_id | `number` | the plugin install ID |
| config | `Object` | config data for this install |
| params | `Object` | The optional URL query params sent in the request |
| location | `EnvoyObject` | the location this event happened \(location installs only\) |
| company | `EnvoyObject` | the company that installed the plugin |
| auth | `Object` | contains the installer's `access_token` |

The SDK uses the `install_id` to create an install-scoped [storage](../the-node.js-sdk/envoypluginstorage.md) located at `req.envoy.installStorage,` and uses the `auth.access_token` to create a user-scoped [api](../the-node.js-sdk/envoyapi.md) instance to make Envoy API calls on behalf of the logged-in user, via `req.envoy.userAPI`.

## If the Request is to a Worker

**Properties**

| Name | Type | Description |
| :--- | :--- | :--- |
| event | `string` | the name of the event |
| plugin\_id | `number` | the plugin ID |
| install\_id | `number` | the plugin install ID |
| config | `Object` | config data for this install |
| job | `EnvoyObject` | the [job]() assigned to this plugin + event |
| location | `EnvoyObject` | the location this event happened \(location installs only\) |
| company | `EnvoyObject` | the company that installed the plugin |
| auth | `Object` | contains the installer's `access_token` |

## Commonalities

Both routes and workers send back some common objects like `location` and `company` that are of type `EnvoyObject`, which represents a resource.

**Properties**

| Name | Type | Description |
| :--- | :--- | :--- |
| id | `string` \| `number` \| `uuid` | the ID of the object |
| attributes | `Object` | the object's attributes, in dash-case. |

