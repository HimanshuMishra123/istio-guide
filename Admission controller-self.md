## Admission Controllers

Admission controllers in Kubernetes enforce policies on objects during their creation or modification. They intercept requests to the Kubernetes API server before objects are persisted and can modify or deny requests based on predefined rules. 
There are 30+ Built-in Admission controllers comes with the Kubernetes. These are compiled into the Kubernetes API server . Some are enabled and some you have to enable. Examples include NamespaceLifecycle, LimitRanger, ResourceQuota, and PodSecurityPolicy.

![alt text](<Service Mesh explained in 60 minutes _ Istio mTLS and Canary Demo _ Complete beginner level guide 55-38 screenshot.png>)


**Dynamic admission controllers** refer to admission controllers that are configured at runtime rather than being hard-coded(default) into the API server. They use webhooks to intercept API server requests, allowing for more flexible and customizable behavior. Istio's admission controllers fall into this category because they use dynamic webhooks to inject sidecars and enforce policies. <br/>

There are two main types of admission controllers:<br/>

Mutating Admission Controllers: These can modify the incoming requests before they are persisted to the etcd (the Kubernetes backing store).<br/>
Validating Admission Controllers: These can validate the incoming requests but cannot modify them. They ensure that the requests adhere to the defined policies.<br/>

In the context of Istio(service meshes) admission controllers are crucial for tasks such as automatic sidecar injection and policy enforcement like VirtualServices and DestinationRules adhere to defined policies.

1. MutatingAdmissionWebhook
This controller intercepts API requests to create or update pods and injects the Envoy sidecar container if the pod is in a namespace labeled for sidecar injection.

2. ValidatingAdmissionWebhook
This controller validates Istio-specific custom resources like VirtualServices and DestinationRules to ensure they are correctly configured.

![alt text](kubp_1701-1.png)

![image](https://github.com/HimanshuMishra123/istio-guide/assets/164254902/7f0a4146-9a79-4618-9d83-f26b6aef288d)

**Working of Webhook**:
The webhook functionality for admission controllers operates as follows-

1. **Kubernetes API Server**:
   - The Kubernetes API server is responsible for invoking admission webhooks.
   - When an API request (e.g., to create a pod) reaches the Kubernetes API server, the server processes built-in admission controllers first.
   - For dynamic admission controllers, if configured, the API server calls the external admission webhooks.

2. **External Webhook Service (Istio in this case)**:
   - The webhook itself is an external HTTP service. In the case of Istio, the webhook is part of the Istio control plane components.
   - For example, the Istio sidecar injector webhook runs as a separate service within the Istio control plane, typically in the `istio-system` namespace.

Here's the flow of how the webhook interaction works between Kubernetes and Istio:

1. **API Request**: A request to create or modify a Kubernetes resource (e.g., a pod) is sent to the Kubernetes API server. the API server first handles authentication and authorization.

2. **Admission Control**:
   - **Built-in Admission Controllers**: The API server first processes any built-in admission controllers.
   - **Dynamic Admission Webhook Controllers**: If configured, Istio leverages dynamic admission controller the Mutating Admission Webhook Controller and the Validating Admission Webhook Controller. the API server forwards the request to the mutating admission webhook endpoint first. This endpoint is specified in the `MutatingWebhookConfiguration` CRD. This configuration specifies rules for the Mutating Admission Webhook Controller, such as:
   - The types of resources it applies to (e.g., pods).
   - The namespace scope.
   - The service endpoint to which the API server should forward the request for mutation.

3. **Mutating Admission Webhook invoked**:
   - The API server sends the admission review request to the Istio sidecar injector webhook service, which is an external HTTP(S) service running within the Kubernetes cluster, managed by Istio.

4. **Webhook Processing**:
   - The Istio webhook service processes the request. For the sidecar injection, it modifies the pod specification to include the sidecar containers.
   - The webhook service then sends the modified admission review response back to the Kubernetes API server.

5. **Validation**:
   - **Validating Admission Webhook invoked**: The API server may also forward the (potentially mutated) request to a validating admission webhook, if configured, to ensure the final object meets all necessary policies.

6. **Persistence**: Once all admission controllers (both built-in and webhook-based) have processed the request, and assuming all validations pass, the API server persists the final resource configuration in etcd.

In summary, the actual webhook services (like Istio's sidecar injector) runs as an external service within the Kubernetes cluster, but the Kubernetes API server is responsible for invoking this service as part of the admission control process which are written in go languages.
