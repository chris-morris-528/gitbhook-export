---
description: 'When Envoy makes requests to your plugin''s endpoints, here''s what to expect.'
---

# Plugin Requests

In the Integration Builder, when you assign a plugin endpoint URL \(as either a [worker or route](../#key-concepts)\), Envoy will make a POST request to those URLs at the appropriate times.

Each request has the following qualities:

* The HTTP method is always a `POST`
* There's always an `X-Envoy-Signature` HTTP header present.
  * See [here](signature-verification.md) for how this signature is verified.
* The request always contains a JSON body.
* The JSON body always contains a `meta` object.
  * See [here](request-meta.md) for more information on this object.
* If the request is to a worker or to a "Validation URL" route, the JSON body will also contain a `payload` object.
  * See [here](request-payload.md) for more information on this object.

