---
description: >-
  When Envoy sends a request to your plugin's worker endpoint or to a
  "Validation URL" route, that request contains a payload describing the subject
  of the request.
---

# Request Payload

The `payload` object is found in the body of every request Envoy makes to your plugin [workers](../#key-concepts). It contains data represents the subject of the event. For example, on the "entry sign in" event, the payload will represent an Envoy `Entry` object.

It is also found in "Validation URL" routes, where the payload represents the data to be validated:

* If the [step type](../../the-integration-builder/#the-form-step-type) is **form**, then the data has keys and values representing each field in the form. The keys are defined in the Integration Builder.
* If the step type is **OAuth2**, then the data is whatever is returned from the authorization flow. Usually this is where you'd find an access token for the service that was authed.
* If the step type is **popup**, then the data is whatever was forwarded from the popup.

In the [Node.js SDK](../the-node.js-sdk/), you can access the `payload` object via `req.envoy.payload.` 

## Event List

Here's the list of available events, and their payload types.

| Name | V1 Name | Type |
| :--- | :--- | :--- |
| Entry sign-in | `entry_sign_in` | Entry |
| Entry sign-out | `entry_sign_out` | Entry |
| Entry block-list review | `entry_blacklist_review` | Entry |
| Entry block-list denied | `entry_blacklist_deny` | Entry |
| Invite created | `invite_created` | Invite |
| Invite updated | `invite_updated` | Invite |
| Invite removed | `invite_removed` | Invite |
| Upcoming invited visit | `upcoming_visit` | Invite |
| Invite QR code sent | `qr_code_sent` | Invite |

## Payload Types

Exact definitions of each type is coming soon.

Note that all fields under `attributes` are dash-cased.

