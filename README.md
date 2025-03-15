
# Kubernetes API for Pod Management

**Kubernetes** provides a **RESTful API** for managing Pods, allowing users to create, update, delete, and monitor them.

# Introduction

**Kubernetes** is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. At the core of Kubernetes architecture lies the Pod, which is the smallest deployable unit. A Pod encapsulates one or more containers, along with shared storage, networking, and runtime configurations.

To interact with and manage Pods programmatically, Kubernetes provides a powerful RESTful API. This API enables developers and administrators to **create, update, delete, monitor, and troubleshoot Pods** efficiently.

In this guide, we will explore the Kubernetes API endpoints for Pod management, covering essential operations like creation, retrieval, updates, deletions, logging, execution of commands, and port forwarding.

## 1. Core Pod API Endpoints

Kubernetes exposes various API endpoints to facilitate Pod management. These endpoints follow the RESTful principles and enable interaction with Kubernetes resources.

### a. List all Pods in a namespace

This endpoint retrieves all Pods running within a specific namespace. It is useful for monitoring and querying running workloads.
```http 
GET /api/v1/namespaces/{namespace}/pods
```
#### Example Request:
```sh
curl -X GET "https://<k8s-api-server>/api/v1/namespaces/default/pods" -H "Authorization: Bearer <token>"
```

### b. Get details of a specific Pod

To fetch detailed information about a particular Pod, including its status, assigned **IP, resource limits, and running containers,** this API is used.
```http 
GET /api/v1/namespaces/{namespace}/pods/{pod-name}
```
A **namespace** in Kubernetes is a logical partition within a cluster that allows users to organize and manage resources separately. It acts as a virtual cluster within a physical Kubernetes cluster, providing isolation for different teams, projects, or environments.
#### Example Request: 

```sh
curl -X GET "https://<k8s-api-server>/api/v1/namespaces/default/pods/mypod" -H "Authorization: Bearer <token>"
```
### c. Create a new Pod

This API allows users to create a new Pod by submitting a JSON manifest that defines the Pod's specifications.
```http
POST /api/v1/namespaces/{namespace}/pods
```

#### Example Pod Manifest (pod.json):
```yaml 
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "mypod",
    "labels": {
      "app": "myapp"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "nginx-container",
        "image": "nginx:latest",
        "ports": [
          {
            "containerPort": 80
          }
        ]
      }
    ]
  }
}
```
#### Example Request:
```sh
curl -X POST "https://<k8s-api-server>/api/v1/namespaces/default/pods" \
     -H "Authorization: Bearer <token>" \
     -H "Content-Type: application/json" \
     -d @pod.json
```
## d. Update an existing Pod (Replace)

Replacing a Pod entirely requires submitting a complete updated manifest. This method is rarely used directly as it deletes and recreates the Pod.
```http
PUT /api/v1/namespaces/{namespace}/pods/{pod-name}
```
### e. Patch a Pod (Partial Update)
For minor modifications such as updating the container image, a **PATCH** request can be used instead of replacing the entire Pod.
```http
PATCH /api/v1/namespaces/{namespace}/pods/{pod-name}
```
#### Example JSON Patch:
```yaml 
[
  {
    "op": "replace",
    "path": "/spec/containers/0/image",
    "value": "nginx:1.21"
  }
]
```
#### Example Request:
```sh 
curl -X PATCH "https://<k8s-api-server>/api/v1/namespaces/default/pods/mypod" \
     -H "Authorization: Bearer <token>" \
     -H "Content-Type: application/json-patch+json" \
     -d '[{"op": "replace", "path": "/spec/containers/0/image", "value": "nginx:1.21"}]'
``` 
### f. Delete a Pod

Pods can be deleted using this API, which gracefully terminates running containers before removing the Pod object.
```http
DELETE /api/v1/namespaces/{namespace}/pods/{pod-name}
```
#### Example Request:
```sh
curl -X DELETE "https://<k8s-api-server>/api/v1/namespaces/default/pods/mypod" -H "Authorization: Bearer <token>"
```
## 2. Watching Pod Events (Real-time Updates)

To track Pod status in real time, the API allows continuous streaming of Pod events.
```sh 
curl -X GET "https://<k8s-api-server>/api/v1/namespaces/default/pods?watch=true" -H "Authorization: Bearer <token>"
```
## 3. Executing Commands in a Running Pod

The Kubernetes API supports running shell commands inside a running Pod, useful for debugging and maintenance.
```http
POST /api/v1/namespaces/{namespace}/pods/{pod-name}/exec
```
#### Example Request:
```sh
kubectl exec mypod -- ls
```
`kubectl` is the **Kubernetes command-line tool** that allows users to interact with the Kubernetes API without needing to craft raw `HTTP` requests. It simplifies communication with the cluster and manages resources efficiently.
## 4. Port Forwarding to a Pod

For direct access to applications inside a Pod, port forwarding can be enabled using the API.
```http 
POST /api/v1/namespaces/{namespace}/pods/{pod-name}/portforward
```
#### Example Request:
```sh
kubectl port-forward mypod 8080:80
```
## 5. Fetching Logs from a Pod

The API provides access to container logs for **debugging purposes.**
```http
GET /api/v1/namespaces/{namespace}/pods/{pod-name}/log
```
#### Example Request:
```sh
kubectl logs mypod
```
Using API:
```sh
curl -X GET "https://<k8s-api-server>/api/v1/namespaces/default/pods/mypod/log" -H "Authorization: Bearer <token>"
```


## Summary of Kubernetes API Endpoints for Pods


|Action | HTTP Method | Endpoint                 |
| :-------- | :------- | :------------------------- |
| List PODS | `GET` | `/api/v1/namespaces/{namespace}/pods` |
| Get Pods details | `GET`| `/api/v1/namespaces/{namespace}/pods/{pod-name}`|
| Create Pods| `POST` | `/api/v1/namespaces/{namespace}/pods`|
|Update Pod	|`PUT	`|`/api/v1/namespaces/{namespace}/pods/{pod-name}`|
|Patch Pod|`	PATCH`|`	/api/v1/namespaces/{namespace}/pods/{pod-name}`|
|Delete Pod|`	DELETE`|`	/api/v1/namespaces/{namespace}/pods/{pod-name}`|
|Watch Pods|`	GET	`|`/api/v1/namespaces/{namespace}/pods?watch=true`|
|Execute in Pod|`	POST	`|`/api/v1/namespaces/{namespace}/pods/{pod-name}/exec`|
|Pod Port Forward|`	POST`|`	/api/v1/namespaces/{namespace}/pods/{pod-name}/portforward`|
|Get Pod Logs|`	GET`|`	/api/v1/namespaces/{namespace}/pods/{pod-name}/log`|


## Conclusion
The Kubernetes API for Pod management provides a comprehensive set of endpoints to interact with and control Pods programmatically. Whether creating new Pods, updating configurations, retrieving logs, or debugging running  applications, these API endpoints form the backbone of Kubernetes automation.

Mastering these APIs is essential for DevOps engineers, cloud developers, and platform administrators looking to streamline Kubernetes-based deployments and ensure high availability of containerized applications.
