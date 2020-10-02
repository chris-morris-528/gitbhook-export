# Plugin Configs

When a customer is going through the setup steps that you've designed in the [Integration Builder](./), the results of each step is saved in a [config](../plugins/#key-concepts) object, which is available to you in both [routes](../plugins/#key-concepts) and [workers](../plugins/#key-concepts).

For [form](./#the-form-step-type) steps, each element in the form will have its results automatically saved in the config object using that element's key \(as designated in the Integration Builder\), which will become a property name in the config object.

## Validation URL Usage

In the Integration Builder, you may provide a validation URL for a step, which Envoy will hit after that step is complete, but before saving the config values. This validation URL allows you to control what gets saved. For example, you may only want a subset of the values to be saved, or you may want to do some processing or validation of the values before saving.

{% hint style="info" %}
Note that for [OAuth2](./#the-oauth-2-step-type) and [Popup](./#the-popup-step-type) step types, a validation URL is **required** in order to save the results of that step. For the Form step type, it is **optional**, and the results of the form will be saved automatically in the absence of a validation URL.
{% endhint %}

### Accessing step results

The results of a step will be in the validation URL's request payload. In the [Node.js SDK](../plugins/the-node.js-sdk/), you can access this under `req.envoy.payload`. 

* For form step types, the payload will be an object whose keys match the keys you set for each form element in the Integration Builder. The values for each key will be the user input.
* For OAuth2 step types, the payload will be the exact results of the OAuth2 flow. This is typically an `access_token` and usually a `refresh_token`.
* For Popup step types, the payload will be whatever query params were passed to the Callback URL.

### Saving values

To save specific values for a step, simply respond to the validation URL request with a JSON object containing the values you wish to save. This object will get merged with the existing config object, ignoring the initial payload's values.

### Stopping a step

You can also validate that the values conform to what you expect. If they are invalid, you can return a 4xx HTTP status, along with an object that has a `message` attribute. If you're using the Node.js SDK, you can use `res.sendFailed`. This will display the message to the user and prevent them from advancing to the next step.

## Config Usage

Once items have been saved in the plugin's config, the config object is accessible in any future route or worker. In the Node.js SDK, it is available under `req.envoy.meta.config`.

If necessary \(for example in non-Envoy requests\), you can get or set the items in the config at any time via API calls in the [Plugin API](../apis/the-plugin-api.md). 



