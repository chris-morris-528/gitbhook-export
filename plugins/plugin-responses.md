---
description: >-
  When Envoy makes requests to your plugin, here are the kinds of responses to
  send back.
---

# Plugin Responses

## For "Validation URL" Routes

When a [setup step](../the-integration-builder/#setup-steps) is validated, Envoy will send a JSON object as the [payload](plugin-requests/request-payload.md) to the URL that you specify.

The response to this should also be a JSON object of data you wish to save in the installation config.

```javascript
app.post('/validate-me', (req, res) => {
  const {
    envoy: {
      payload: {
        foo,
      },
    }
  } = req;
  res.send({
    foo, // we will save the original "foo" from the payload
    bar: 'hello world', // along with a new "bar" variable
  });
});
```

You may also choose to not to save any data in the config, and instead opt for [storage](./#key-concepts) instead.

```javascript
app.post('/validate-me', async (req, res) => {
  const {
    envoy: {
      installStorage,
      payload: {
        password,
      },
    }
  } = req;
  await installStorage.set('password', password);
  res.send({});
});
```

You can also fail the validation by sending back a 4xx or 5xx error, along with a message to display to the installer. 

```javascript
app.post('/validate-me', (req, res) => {
  res.status(400).send({ message: 'These values are bad.' });
});
```

For convenience, the Node.js SDK has a helper function for this.

```javascript
app.post('/validate-me', (req, res) => {
  res.sendFailed('These values are bad.');
});
```

## For "Options URL" Routes

In order to populate dropdowns during setup, the Integration Builder will ask for a URL where it can fetch an array of options. These options are arrays of objects with a `label` and `value`. The label will be what is displayed, while the value will be saved in the installation config.

```javascript
app.post('/options', (req, res) => {
  res.send([
    { label: 'Foo', value: 1 },
    { label: 'Bar', value: 2 },
  ]);
});
```

## For Job Workers

These event handlers should respond in the context of the job. That is, either the job is ongoing, successful, ignored, or failed.

Reporting job status can be done in 2 ways, either based on the HTTP response code \(the simple way\), or as explicit API calls \(for more advanced use cases, covered later\).

### Simple Job Reporting

#### Success

To report a job as a success, you simply need to respond with a `200` or `201` HTTP status code. The response body is not important and is only used for later debugging if necessary.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.send({ foo: 'bar' }); // the "entry sign in" job will be reported as a success.
});
```

####  Ongoing

To report a job as ongoing, respond with a `202` status code. The response body is not important and is only used for later debugging if necessary. Ongoing jobs are used in more advanced multi-step situations \(shown later\).

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.status(202).send({ foo: 'bar' });
});
```

If you're using the Node.js SDK middleware, there is also a helper `res.sendOngoing` function.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.sendOngoing({ foo: 'bar' });
});
```

#### Ignored

To report a job as ignored, respond with a `400` status code. The response body should be an object with a `message` property that describes why the job was ignored.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.status(400).send({ message: "We don't want to do this one." });
});
```

If you're using the Node.js SDK middleware, there is also a helper `res.sendIgnored` function.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.sendIgnored("We don't want to do this one.");
});
```

#### Failed

To report a job as failed, respond with a `412` status code. The response body should be an object with a `message` property that describes why the job failed.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.status(412).send({ message: "You're missing something critical." });
});
```

If you're using the Node.js SDK middleware, there is also a helper `res.sendFailed` function.

```javascript
app.post('/entry-sign-in', (req, res) => {
  res.sendFailed("You're missing something critical.");
});
```

#### Unintentional Failures

If Envoy receives any status code other than those listed above, it will be an assumed job failure. The Node.js SDK's [`errorMiddleware`]() helps these situations by serializing errors to JSON and setting a `500` status code.

### Advanced Job Reporting

Sometimes responding to a job event is a multi-step process. For example, if you're dependent on an external API to perform a long-running task, you can count the queueing of the task as one step in that process, and the eventual result of that task as the final step. In these situations, you can use API calls to report job statuses.

In the following example, we have an "entry sign in" worker, who enqueues a task into some fake service that operates on the entry, and hits a callback URL once it's complete. We utilize the [JWT helper](the-node.js-sdk/#full-list-of-exports) from the Node.js SDK in order to encode the job ID, which we pass as a query param into the callback URL. We then report that the job is ongoing.

In the callback URL handler, we get the token from the URL query params and decode it to get the job ID back. This is necessary because this callback URL was not invoked by Envoy, and therefore has none of the typical [request attributes](plugin-requests/). Therefore we need to manually create the job object that was present in the original worker by using the job ID along with the `getJob` SDK method.

Once we have this job context back, we can report that the job has been completed via API. We could've also reported other statuses. Finally, we respond with anything to inform the fake service we successfully received the callback. More likely, we would've also used the task results to attach some data to the job, to display in the Envoy dashboard.

```javascript
app.post('/entry-sign-in', (req, res) => {
  const {
    envoy: {
      jwt,
      job,
      payload: entry,
    },
  } = req;
  const token = await jwt.encode(job.id);
  const enqueueResult = await enqueueTask({
    operateOn: entry,
    callback: `https://my-plugin-url.com/task-callback?token=${token}`,
  });
  res.sendOngoing('Task enqueued.', { enqueueResult });
});

app.get('/task-callback', (req, res) => {
  const {
    envoy: {
      jwt,
      getJob,
    },
    body: taskResult,
    params: {
      token,
    },
  } = req;
  const { sub: jobId } = await jwt.decode(token);
  const job = getJob(jobId);
  await job.complete('Task finished.');
  res.send('OK');
});
```

