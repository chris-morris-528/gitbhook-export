# The Integration Builder

The integration builder is a plugin that builds other plugins. Use it to build integrations that are private to your company. Private integrations can then be upgraded to public after review.

## Usage

After successfully enrolling into Envoy's developer program, you will be granted access to the Integration Builder. It will appear in the "Build Your Own" section of the Integrations page. Once it is available in the Envoy dashboard, install it. The final step of installation will open up a popup window with the actual builder. Click on "create new" to begin.

The builder is broken up into four sections: Details, Events, Setup Steps, and API Credentials.

### Details

Fairly self-explanatory. Note that for installable plugins, typically you'll want to complete the builder with the "live" checkbox unchecked, until you receive your API credentials \(after saving\). Once you've put in your API credentials into your plugin environment, you can then go live. Going live makes your plugin available in the Envoy dashboard in the Integrations page, private to your company. Only TSEs will be able to turn private plugins into public ones \(available to all companies\).

#### API Scopes

You'll notice that when you check the "Installable plugin in Envoy's dashboard" box, you'll get an \(optional\) dropdown to select API scopes. This is useful if your plugin makes any API calls to Envoy's API. During the customer's plugin setup, they will be asked to authorize those scopes. Once they accept, your plugin will receive a valid, scoped token to make API calls with on behalf of that user whenever Envoy sends a request to your plugin.

### Events

These are the events you want your plugin to listen for and react to. Subscribing to an event is as simple as putting in your plugin [worker's](../plugins/#key-concepts) endpoint URL. When the relevant event happens, we'll send a POST request to that URL. You can learn more about what to expect in those requests [here](../plugins/plugin-requests/).

### Setup Steps

Each step you create represents a screen you wish the plugin installer to be presented with. These steps are how you gather the necessary information from the installer in order for your plugin to function properly.

For example, the installer may be asked to supply their preferred configuration, or they may be asked to authenticate with some external service that your plugin talks to.

A step can either be a **form** that the user fills out, or an **OAuth2** dialog to authenticate with, or a **popup** hosting custom content. Depending on the step type, the results are handled differently.

#### The "form" step type

When the installer completes a **form** step, their values are automatically saved to the installation config, using the "Key" you specified as the key to access that value in the request meta's `config` property.

If you wish to take control of which values are saved \(if any\), you can supply a "Validation URL", which Envoy will hit as a [route](../plugins/#key-concepts).

The response to this request should be an object to be used as the keys and values will be saved in the installation config. To prevent the step from completing, send back a `400` response. If your response is a JSON object that contains a `message` property, we will use that as the error message to display to the installer.

#### The "OAuth2" step type

When the installed completes an **OAuth2** step, Envoy automatically handles the OAuth2 flow. At the end of the flow, you can save the resulting access token and any other resulting data by supplying a "Validation URL". Note that unlike the **form** step type, using a validation URL is the only way to save these values.

For this step type, it often makes sense to favor storing data in [storage](../plugins/#key-concepts) instead of sending any objects back to store in the installation config, as storage is the best place for sensitive data \(it is never exposed to the front-end\).

Also, be sure to register the "Callback URL" found in the builder as a valid `redirect_uri` within the external system you're authing against.

#### The "popup" step type

This step type should be used when you wish to take full control of some external auth. You can even rebuild an OAuth2 flow using it.

To create a popup, supply a "Content URL". When the installer arrives at this step, we will display a popup redirected to this URL. Additionally, we will append a `state` query parameter to the URL.

When you've completed your flow, redirect the user back to the "Callback URL" found in the builder, with the same `state` query parameter appended to that callback URL. This redirect will close the popup and complete the step. If you wish to save any values for this step, pass them as additional query params.

For complex data such as nested objects, or for sensitive data that you wish to save in storage, you can utilize the "Validation URL" like the other step types.

### Form fields

Here's the list of available field types that a **form** step can have:

* **text** - typical text input
* **checkbox** - typical checkbox
* **link** - a button that opens a link in either a new tab or a popup
* **info** - static text
* **select** - a dropdown whose options are populated by an endpoint in your plugin
* **resource select** - pre-made dropdowns to select Envoy resources \(locations, flows, employees, etc.\)
* **mapping** - maps a list of options to some other data type

Most field types are self-explanatory, so below we will explore the more complex ones.

#### Select

Select boxes display a list of options. To create a select box, you must supply an "Options URL", which Envoy will hit in order to load the select options. You must respond to this with a JSON array of objects, where each object must have a `label` and `value`.

Select boxes have the option to be searchable and paginated. A searchable select will have a search bar present in the dropdown. A paginated select will continuously load more options as you scroll down the dropdown. Selects can be both searchable and paginated at the same time as well.

When a dropdown is searchable, the call to the Options URL will include a `search` param. When paginated, the call to that URL will include a `page` param. The plugin is responsible for answering that request with the appropriate array of option objects \(objects with a `label` and `value`\) based on those params.

When pagination is enabled, the Integration Builder will ask for an additional URL for "selected values". This is useful for cases where that form field already has a value associated with it from a previous save, but that value is not present in the first page of options. Envoy will make a request to that URL with a `value` param to load the appropriate option object for that value and insert it as an option.

#### Advanced Pagination using Cursors

As an alternative to _paged_ pagination, Envoy also supports _cursored_ pagination, where instead of page numbers, pagination is done though tokens. It works as follows:

Envoy's initial call to retrieve the first page of data will contain no params. The plugin should respond with an object instead of an array of options. This object should include a `data` property with the array of options, and a `cursor` property. Whatever value is in this `cursor` property will then be passed back to the plugin when Envoy requests the next page of data, as a `cursor` param.

Using cursored pagination is useful for wrapping APIs that use cursors, or for displaying consistent dropdowns for actively changing data.

Read more about [cursors](https://dev.to/jackmarchant/offset-and-cursor-pagination-explained-b89) here.

#### The Mapping Field Type

The **mapping** type is a bit more complex than **select**, but addresses a very common use case. It generates a mapping of one set of data to another. For example, you can map Envoy visitor types to an external system's user types.

For the most part, the **mapping** type functions like a **select** type, where you must present an array of options to render a dropdown. This dropdown will appear on the left side of the screen. You will be asked to select a secondary field type, which will appear on the right side of the screen. The installer will then be able to select an option from the left dropdown to map to a value on the right. The installer can add as many mappings as they like.

### API Credentials

The API credentials are a client ID and client secret assigned to your plugin. They are only generated after the first save of your plugin, and are used by your plugin to generate plugin-scoped access tokens.

These access tokens are special in that they are exclusively used to perform plugin actions like saving items to storage, or updating a job. They are also used to verify the signature that Envoy sends along with each request to your plugin. The Node.js SDK uses these values automatically, once they are present in the `ENVOY_CLIENT_ID` and `ENVOY_CLIENT_SECRET` environment variables. Locally, you can create a `.env` file to define these variables.

## Next Steps

Once you've defined your events and setup steps and filled in your client credentials environment variables, you can take your plugin live by checking the "live" checkbox under the Details section and re-saving the plugin.

Once you've done that, your plugin will appear in the Integrations page for all locations in your company. Plugins can then go public to all companies via a TSE.

