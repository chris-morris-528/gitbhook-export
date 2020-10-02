---
description: >-
  When Envoy makes requests to your plugin's endpoints, we send along a
  signature you can use to verify the request.
---

# Signature Verification

An important part of plugin security is preventing unwanted requests to your endpoints from bad actors.

In order to verify that a request came from Envoy, we also send a _signature_ in the `X-Envoy-Signature` header. This signature is a Base64-encoded string that represents a [SHA-256 HMAC](https://en.wikipedia.org/wiki/HMAC) of the request body, using your plugin client secret as the key.

You can then verify the signature by building your own signature of the request body signed with your client secret, and comparing the two signatures. The [Node.js SDK](../the-node.js-sdk/) middleware does this verification automatically on each request, and prevents access to the request meta and event payloads unless the request is verified.

