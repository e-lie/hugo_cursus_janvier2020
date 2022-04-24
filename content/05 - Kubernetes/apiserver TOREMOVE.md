---
title: 10 - Principes de conception et architecture détaillée de Kubernetes
draft: true
weight: 2100
---
## API server

Although etcd is at the core of a Kubernetes cluster, there is actually only a single server that is allowed to have direct access to the Kubernetes cluster, and that is the API server. The API server is the hub of the Kubernetes cluster; it mediates all interactions between clients and the API objects stored in etcd. Consequently, it is the central meeting point for all of the various components. The API server implements a RESTful API over HTTP, performs all API operations, and is responsible for storing API objects into a persistent storage backend.

Operating the Kubernetes API server involves three core funtions:

- API management : The process by which APIs are exposed and managed by the server
- Request processing : The largest set of functionality that processes individual API requests from a client
- Internal control loops : Internals responsible for background operations necessary to the successful operation of the API server

### Les routes web de et la découverte de l'API

L'API a des routes systématiques pour les resources:

Here are the components of the two different paths for namespaced resource types:

- `/api/v1/namespaces/<namespace-name>/<resource-type-name>/<resource-name>`
- `/apis/<api-group>/<api-version>/namespaces/<namespace-name>/<resource-type-name>/<resource-name>`

Here are the components of the two different paths for non-namespaced resource types:

- `/api/v1/<resource-type-name>/<resource-name>`
- `/apis/<api-group>/<api-version>/<resource-type-name>/<resource-name>`


Now we would like to explore the API and see how it gives information for its own exploration. To explore a REST API the classic `curl` tool can be sufficent.

To make it easy to explore the API server, run the kubectl tool in proxy mode to expose an unauthenticated API server on localhost:8001 using the following command: `kubectl proxy`

Then to see how the API gives you many informations like every resources in each `api-group` you can launch: `curl localhost:8001/api` or `curl localhost:8001/api/v1`

To access the web page for the whole api specification (like any swagger/openAPI endpoint) open in a browser: `localhost:8001/openapi/v2`

### Version d'un API Group

- `v1alpha` : unstable and not suitable for production use cases. the API surface area may change between Kubernetes releases and that the implementation of the API itself may be unstable and may even destabilize the entire Kubernetes cluster

- `v1beta` :  The beta designation indicates that the API is generally stable but may have bugs or final API surface refinements. In general, beta APIs are assumed to be stable between Kubernetes releases, and backward compatability is a goal but not always achieved. Ths features are enabled in many clusters but should be used with care.

- `v1` General availability (GA) : indicates that the API is stable. These APIs come with both a guarantee of backward compatability and a deprecation guarantee. After an API is marked as scheduled forremoval, Kubernetes retains the API for at least three releases.

### Life of a Request

To better understand what the API server is doing for each of these different requests, we’ll take apart and describe the processing of a single request to the API server.

#### Authentication

The first stage of request processing is authentication, which establishes the identity associated with the request. The API server supports several different modes of establishing identity, including client certificates, bearer tokens, and HTTP Basic Authentication. In general, client certificates or bearer tokens, should be used for authentication; the use of HTTP Basic Authentication is discouraged.

In addition to these local methods of establishing identity, authentication is pluggable, and there are several plug-in implementations that use remote identity providers. These include support for the OpenID Connect (OIDC) protocol, as well as Azure Active Directory. These authentication plug-ins are compiled into both the API server and the client libraries. This means that you may need to ensure that both the command-line tools and API server are roughly the same version

#### RBAC/Authorization

After the API server has determined the identity for a request, it moves on to authorization for it. Every request to Kubernetes follows a traditional RBAC model. To access a request, the identity must have the appropriate role associated with the request. Kubernetes RBAC is a rich and complicated topic, and as such, we have devoted an entire chapter to the details of how it operates. For the purposes of this API server summary, when processing a request, the API server determines whether the identity associated with the request can access the combination of the verb and the HTTP path in the request. If the identity of the request has the appropriate role, it is allowed to proceed. Otherwise, an HTTP 403 response is returned. This is covered in much more detail in a later chapter.

#### Admission control

After a request has been authenticated and authorized, it moves on to admission control. Authentication and RBAC determine whether the request is allowed to occur, and this is based on the HTTP properties of the request (headers, method, and path). Admission control determines whether the request is well formed and potentially applies modifications to the request before it is processed. Admission control defines a pluggable interface:

`apply(request): (transformedRequest, error)`

If any admission controller finds an error, the request is rejected. If the request is accepted, the transformed request is used instead of the initial request. Admission controllers are called serially, each receiving the output of the previous one. Because admission control is such a general, pluggable mechanism, it is used for a wide variety of different functionality in the API server. For example, it is used to add default values to objects. It can also be used to enforce policy (e.g., requiring that all objects have a certain label). Additionally, it can be used to do things like inject an additional container into every Pod. The service mesh Istio uses this approach to inject its sidecar container transparently. Admission controllers are quite generic and can be added dynamically to the API server via webhook-based admission control.

### Specialized requests

In addition to the standard RESTful requests, the API server has a number of specialized request patterns that provide expanded functionality to clients:

`/proxy`, `/exec`, `/attach`, `/logs`

The first important class of operations is open, long-running connections to the API server. These requests provide streaming data rather than immediate responses.

`logs` is the easiest streaming request to understand because it simply leaves the request open and streams in more data. The rest of the operations take advantage of the WebSocket protocol for bidirectional streaming data.

On top of those streams, the Kubernetes API server actually introduces an additional multiplexed streaming protocol. The reason for this is that, for many use cases, it is quite useful for the API server to be able to service multiple independent byte streams. Consider, for example, executing a command within a container. In this case, there are actually three streams that need to be maintained (stdin, stderr, and stdout).

#### Watch operations

In addition to streaming data, the API server supports a watch API. A watch monitors a path for changes. Thus, instead of polling at some interval for possible updates, which introduces either extra load (due to fast polling) or extra latency (because of slow polling), using a watch enables a user to get low-latency updates with a single connection. When a user establishes a watch connection to the API server by adding the query parameter `?watch=true` to some API server request

#### The CRD and their control loop

Custom resource definitions (CRDs) are dynamic API objects that can be added to a running API server. Because the act of creating a CRD inherently creates new HTTP paths the API server must know how to serve, the controller that is responsible for adding these paths is colocated inside the API server. With the addition of delegated API servers (described in a later chapter), this controller has actually been mostly abstracted out of the API server. It currently still runs in process by default, but it can also be run out of process.

## Debugging the API Server

Of course, understanding the implementation of the API server is great, but more often than not, what you really need is to be able to debug what is actually going on with the API server (as well as clients that are calling in to the API server). The pri mary way that this is achieved is via the logs that the API server writes.

### Basic Logs
By default, the API server logs every request that is sent to the API server. This log includes the client’s IP address, the path of the request, and the code that the server returned. If an unexpected error results in a server panic, the server also catches this panic, returns a 500, and logs that error.

I0803 19:59:19.929302
 1 trace.go:76] Trace[1449222206]:
"Create /api/v1/namespaces/default/events" (started: 2018-08-03
19:59:19.001777279 +0000 UTC m=+25.386403121) (total time: 927.484579ms):
Trace[1449222206]: [927.401927ms] [927.279642ms] Object stored in database
I0803 19:59:20.402215
 1 controller.go:537] quota admission added
evaluator for: { namespaces}

In this log, you can see that it starts with the timestamp I0803 19:59:... when the log line was emitted, followed by the line number that emitted it, trace.go:76, and finally the log message itself.

### Audit Logs

The audit log is intended to enable a server administrator to forensically recover the state of the server and the series of client interactions that resulted in the current state of the data in the Kubernetes API. For example, it enables a user to answer questions like, “Why was that ReplicaSet scaled up to 100?”, “Who deleted that Pod?”, among others. Audit logs have a pluggable backend for where they are written.

### Activating Additional Logs

Kubernetes uses the github.com/golang/glog leveled logging package for its logging. Using the --v flag on the API server you can adjust the level of logging verbosity. In general, the Kubernetes project has set log verbosity level 2 (--v=2)

In addition to debugging the API server via logs, it is also possible to debug interactions with the API server, via the kubectl command-line tool. Like the API server, the kubectl command-line tool logs via the github.com/golang/glog package and supports the --v verbosity flag. Setting the verbosity to level 10 (`--v=10`)

