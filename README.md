## Node.js Cloud Events Stack

**THIS IS A PROOF OF CONCEPT IMPLEMENTATION**

This stack provides a Node.js framework for executing a function that
can respond to Cloud Events.

The function is loaded, and then invoked for incoming HTTP requests
at `localhost:8080`. The incoming request may be a
[Cloud Event](https://github.com/cloudevents/sdk-javascript#readme.) or
just a simple HTTP GET request. In either case, the function will receive
a `Context` object instance that has an `event` property. For a raw HTTP
request, the incoming request is converted to a Cloud Event.

The invoked user function can be `async` but that is not required.

TBD: What format should the function response be? Should it also be a Cloud
Event? But where is the event emitted? To some Knative Channel? Much is
still to be determined.

The stack will expose three endpoints.

  * `/` The endpoint for the user function.
  * `/health/readiness` The endpoint for the readiness health check
  * `/health/liveness` The endpoint for the liveness health check

This stack is based on `Node.js v12`.

### Example

```js
module.exports = async function myFunction(context) {
  const ret = 'This is a test function for Node.js FaaS. Success.';
  return new Promise((resolve, reject) => {
    setTimeout(_ => {
      resolve(ret);
    }, 500);
  });
};
```

You can use `curl` to `POST` to the endpoint:
```console
$ curl -X POST -d 'hello=world' \
  -H'Content-type: application/x-www-form-urlencoded' http://localhost:8080
```

You can use `curl` to `POST` JSON data to the endpoint:
```console
$ curl -X POST -d '{"hello": "world"}' \
  -H'Content-type: application/json' \
  http://localhost:8080
```

You can use `curl` to `POST` an event to the endpoint:
```console
$ curl -X POST -d '{"hello": "world"}' \
  -H'Content-type: application/json' \
  -H'Ce-id: 1' \
  -H'Ce-source: cloud-event-example' \
  -H'Ce-type: dev.knative.example' \
  -H'Ce-specversion: 0.2' \
  http://localhost:8080
```

## Templates

Templates are used to create your local project and start your development. When initializing your project you will be provided with a very simple function implementation.

This template only has a simple `index.js` file which implements the `/` endpoint. The application metadata is provided via a package.json file.

## Getting Started

1. Create a new folder in your local directory and initialize it using the Appsody CLI, e.g.:

    ```bash
    mkdir my-project
    cd my-project
    appsody init experimental/node-ce-functions
    ```

    This will initialize a Node.js Cloud Event functions project using the default template.

**NOTE:** If you encounter the following error, [configure the experimental repo](#Configuring-Experimental-Repo):

**`[Error] Repository experimental is not in configured list of repositories`**.

2. After your project has been initialized you can then run your application using the following command:

    ```bash
    appsody run
    ```

    This launches a Docker container that continuously re-builds and re-runs your project, exposing it on port 8080.

    You can continue to edit the application in your preferred IDE (VSCode or others) and your changes will be reflected in the running container within a few seconds.

3. You should be able to access the following endpoints, as they are exposed by your template application by default:

    - Application endpoint: http://localhost:8080/
    - Liveness endpoint: http://localhost:8080/liveness/liveness
    - Readiness endpoint: http://localhost:8080/health/readiness


## Configuring Experimental Repo

Upgrade your CLI to the latest version and add the experimental repo:

1. `brew upgrade appsody` or for other platforms visit the [upgrading appsody section](https://appsody.dev/docs/getting-started/installation).

2. appsody repo add experimental https://github.com/appsody/stacks/releases/latest/download/experimental-index.yaml

You should now be able to [initialise your application](#Getting-Started).

## License

This stack is licensed under the [Apache 2.0](./image/LICENSE) license
