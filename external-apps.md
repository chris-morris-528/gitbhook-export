# External Apps

The Integration Builder can also be used to provision external applications. These are apps that leverage the Envoy API in some way, but are not installed from the Integrations page. Instead, they are for 3rd parties to integrate into their own systems.

To provision an external app, simply check the "External application using OAuth2" checkbox in the Integration Builder, and add a "redirect URI", which is a URL that we will redirect to during the OAuth2 flow. The resulting client ID and secret can then be used to authenticate with Envoy's API using the "authorization\_code" grant type.

## **OAuth2 Flow** <a id="oauth2-flow"></a>

* Mutual customers of the 3rd party and Envoy can be redirected to Envoy's OAuth2 dialog box to ask for the relevant scopes. e.g. "locations.read", "flows.read", "invites.write", "token.refresh".
* If the customer accepts, they will be redirected back to the redirect URL, where the application may exchange the resulting authorization code for an Envoy access token and refresh token, which should be saved.
* The application may then use the access token to access Envoy's API.
* When the access token expires, use the refresh token to receive a new access token.

### **Relevant API calls** <a id="relevant-api-calls"></a>

* Redirect to Envoy OAuth2 dialog: [https://app.envoy.com/a/auth/v0/authorize?response\_type=code&client\_id={ENVOY\_CLIENT\_ID}&redirect\_uri={REDIRECT\_URL}&scope=locations.read+flows.read+invites.write+token.refresh](https://app.envoy.com/a/auth/v0/authorize?response_type=code&client_id=%7BENVOY_CLIENT_ID%7D&redirect_uri=%7BTALKPUSH_REDIRECT_URL%7D&scope=locations.read+flows.read+invites.write+token.refresh)​
  * Note that in the above example, the scopes are space-separated, but have been URL-encoded, resulting in the `+` signs. Please do not double-encode the `+` signs.
* Once the customer accepts, we will redirect to {REDIRECT\_URL} with a "code" query param. Exchange this code for access token and refresh token:
  * URL: [https://app.envoy.com/a/auth/v0/token](https://app.envoy.com/a/auth/v0/token)​
  * Method: `POST`
  * Content-Type: `application/json`
  * Body: `{ "grant_type": "authorization_code", "code": {CODE_FROM_OAUTH2}, "client_id": {ENVOY_CLIENT_ID}, "client_secret": {ENVOY_CLIENT_SECRET} }`
* to get a new access token if expired:
  * URL: [https://app.envoy.com/a/auth/v0/token](https://app.envoy.com/a/auth/v0/token)​
  * Method: `POST`
  * Content-Type: `application/json`
  * Body: `{ "grant_type": "refresh_token", "refresh_token": {REFRESH_TOKEN}, "client_id": {ENVOY_CLIENT_ID}, "client_secret": {ENVOY_CLIENT_SECRET} }`

