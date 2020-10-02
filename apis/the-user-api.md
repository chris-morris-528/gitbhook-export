# The User API

The full user-scoped REST API is documented here: [https://documenter.getpostman.com/view/1368426/RWgp2L8z?version=latest\#fe9d0c29-e9bf-4b3c-abde-9b6e2924c13f](https://documenter.getpostman.com/view/1368426/RWgp2L8z?version=latest#fe9d0c29-e9bf-4b3c-abde-9b6e2924c13f)

Plugins receive user-scoped access tokens during each event, but you may additionally request a fresh token with the following method:

{% api-method method="post" host="https://app.envoy.com" path="/a/auth/v0/token" %}
{% api-method-summary %}

{% endapi-method-summary %}

{% api-method-description %}
Request a user-scoped access token for a given install. The user the token represents will be the installer of the plugin.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}

{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="install\_id" type="string" required=false %}
The install ID.
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
Set to "plugin\_install".
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

