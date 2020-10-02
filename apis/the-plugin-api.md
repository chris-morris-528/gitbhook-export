---
description: >-
  These are plugin-scoped API methods. They are already implemented in our
  Node.js SDK.
---

# The Plugin API

{% api-method method="post" host="https://app.envoy.com" path="/a/auth/v0/token" %}
{% api-method-summary %}
Login
{% endapi-method-summary %}

{% api-method-description %}
Request a plugin-scoped access token.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
Basic auth using the client ID and secret as the username and password.
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="client\_id" type="string" required=true %}
Your client ID from the Builder
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
Your client secret from the Builder
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
Set to "client\_credentials"
{% endapi-method-parameter %}

{% api-method-parameter name="scope" type="string" required=true %}
Set to "plugin,token.refresh"
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
OAuth2 Response
{% endapi-method-response-example-description %}

```javascript
{
    "token_type": "Bearer",
    "access_token": "foo",
    "expires_in": 43200,
    "refresh_token": "bar",
    "refresh_token_expires_in": 259200,
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="patch" host="https://app.envoy.com" path="/api/v2/plugin-services/jobs/:jobId" %}
{% api-method-summary %}
Update Job
{% endapi-method-summary %}

{% api-method-description %}
Perform job updates
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="jobId" type="string" required=true %}
The job id.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-body-parameters %}
{% api-method-parameter name="status" type="string" required=true %}
Either "done", "ignored", or "failed".
{% endapi-method-parameter %}

{% api-method-parameter name="status\_message" type="string" required=true %}
A message describing why the status was set.
{% endapi-method-parameter %}

{% api-method-parameter name="failure\_reason" type="string" required=false %}
If the job  status is "failed" or "ignored", set this as well.
{% endapi-method-parameter %}

{% api-method-parameter name="attachments" type="array" required=false %}
Array of objects with a label, type, and value.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
  "data": {
    "id": "18d84a69-acd2-4b61-9dd4-f95b3b589283",
    "name": "entry_sign_in",
    "identifier": "vr:entry:19"
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.envoy.com" path="/api/v2/plugin-services/storage" %}
{% api-method-summary %}
Storage Pipeline
{% endapi-method-summary %}

{% api-method-description %}
Send commands to the key/value storage.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="install\_id" type="string" required=false %}
Optionally scope the key to this installation.
{% endapi-method-parameter %}

{% api-method-parameter name="commands" type="array" required=true %}

{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
An array of items, one for each command in the request.
{% endapi-method-response-example-description %}

```javascript
{
  "data": [
    {
      "key": "foo",
      "value": "bar"
    }
  ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="https://app.envoy.com" path="/api/v2/plugin-services/installs/:installId/config" %}
{% api-method-summary %}
Get Plugin Config
{% endapi-method-summary %}

{% api-method-description %}
Fetches the plugin config object.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="installId" type="string" required=true %}
The ID of the installation whose config you want
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
The complete config object.
{% endapi-method-response-example-description %}

```javascript
{
  "data": {}
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="put" host="https://app.envoy.com" path="/api/v2/plugin-services/installs/:installId/config" %}
{% api-method-summary %}
Set Plugin Config
{% endapi-method-summary %}

{% api-method-description %}
The body of the request should be the new config items you wish to merge into the existing config. To remove an existing item, set its value to `null`.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="installId" type="string" required=true %}
The ID of the installation whose config to set
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
The complete config object.
{% endapi-method-response-example-description %}

```
{
  "data": {}
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

