# JavaScript client for Kubernetes API

This client is intended for use in the browser (not tested in node). It uses the fetch API and runs in all modern browsers. For older browsers, the fetch polyfil (https://github.com/github/fetch) should be used.

The k8sclient enables communication with the Kubernetes API. In doing so, the client largely dispenses with the interpretation of the data and adds its own minimally necessary logic. In addition to the standard HTTP methods such as GET or POST, it implements the WATCH method, which sets up a stream to the server and reacts to the events.

The client expects a Kubernetes API endpoint and a token. As soon as both parameters have been provided, the following functions are available:

- head
- get
- post
- put
- patch
- delete
- watch

# Installation

With npm:

```
npm install @sapcc/k8sclient
```

With yarn:

```
yarn add @sapcc/k8sclient
```

# Example Code (ES6)

## List all pods

```js
import { createClient } from "core/k8sClient"

const apiEndpoint = "https://k8s-api.com"
let token = "BEARER-TOKEN"

const apiClient = createClient({ apiEndpoint, token })

apiClient.get("/api/v1/pods").then((data) => console.log(data))
```

## Create a new namespace

```js
import { createClient } from "core/k8sClient"

const apiEndpoint = "https://k8s-api.com"
let token = "BEARER-TOKEN"

const apiClient = createClient({ apiEndpoint, token })

apiClient.post("/api/v1/namespaces", {
  apiVersion: "v1",
  kind: "Namespace",
  metadata: {
    name: "my_namespace",
    annotations: { displayName: "My Namespace" },
    labels: { app: "my-app" },
  },
})
```

## Delete a namespace

```js
import { createClient } from "core/k8sClient"

const apiEndpoint = "https://k8s-api.com"
let token = "BEARER-TOKEN"

const apiClient = createClient({ apiEndpoint, token })

apiClient.delete("/api/v1/namespaces/my_namespace")
```

## Refresh Token

```js
import { createClient } from "core/k8sClient"

const apiEndpoint = "https://k8s-api.com"
let token = "BEARER-TOKEN"

const apiClient = createClient({ apiEndpoint, token })

apiClient.refreshToken("NEW-TOKEN")
```

## Watch

```js
import { createClient } from "core/k8sClient"

const apiEndpoint = "https://k8s-api.com"
let token = "BEARER-TOKEN"

const apiClient = createClient({ apiEndpoint, token })
const dispatch = (action) => console.log(action)

const podsWatch = apiClient
  .watch("/api/v1/pods")
  .on(apiClient.WATCH_ERROR, () => console.log("ERROR"))
  .on(apiClient.WATCH_ADDED, (items) =>
    dispatch({ type: "RECEIVE_ITEMS", items })
  )
  .on(apiClient.WATCH_MODIFIED, (items) =>
    dispatch({ type: "UPDATE_ITEMS", items })
  )
  .on(apiClient.WATCH_DELETED, (items) =>
    dispatch({ type: "DELETE_ITEMS", items })
  )

podsWatch.start()

setTimeout(podsWatch.cancel, 5 * 60 * 1000) // 5 minutes
```

# Development

All dependencies of this project are expressed in its package.json file. Before you start developing, ensure that you have NPM installed, then run:

```bash
npm run build
npm run test
```
