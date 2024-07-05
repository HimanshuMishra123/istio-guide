
![image](https://github.com/HimanshuMishra123/istio-guide/assets/164254902/143db1e6-7af0-4bcd-baf6-086ea982356a)

You do not need to install the `istiod` control plane on every node in your Kubernetes cluster. <br/>
The Istio control plane components are deployed in a central location within your Kubernetes cluster on any one worder node in a separate namespace 'istio-system' and it does not need to be installed on every node.<br/>
The primary Istio control plane pod is `istiod`, but depending on the configuration, you might also find other auxiliary pods and services, such as the Istio ingress and egress gateways. <br/>
The data plane components (sidecars and gateways) are distributed across nodes, while `istiod` handles the configuration and management centrally from control plane.<br/>

### Deployment of `istiod`

1. **Centralized Control Plane**: 
   - **Deployment**: The `istiod` control plane component is deployed as a single or a set of pods (typically one per node, but it can be scaled horizontally if needed) in a designated namespace, usually `istio-system`.
   - **Node Distribution**: `istiod` does not need to run on every node. It runs in a central location within the cluster, and its services are exposed to all nodes via Kubernetes services.

2. **Service Discovery**: 
   - **Communication**: Nodes and the sidecar proxies on the pods communicate with `istiod` using Kubernetes services. The control plane components manage the configuration, traffic policies, and certificates centrally, and the data plane proxies (Envoy sidecars) get their configuration from `istiod` through the service discovery mechanisms.

### Deployment of Data Plane

1. **Sidecar Proxies**: 
   - **Injection**: The sidecar proxies, such as Envoy, are injected into application pods as part of the deployment process. These proxies are distributed across all nodes where application pods are scheduled.
   - **Local Communication**: The sidecar proxies on each node communicate with the `istiod` control plane for configuration updates and policy enforcement. 

2. **Gateway Proxies**: 
   - **Ingress/Egress Gateways**: If you are using Istio’s ingress or egress gateways, these are deployed as separate pods and can be scheduled on specific nodes or across multiple nodes depending on the configuration and resource needs.

### Benefits of Centralized Control Plane

- **Resource Efficiency**: By running `istiod` as a centralized service rather than on every node, you save resources and simplify management.
- **Scalability**: You can scale `istiod` horizontally if necessary, depending on the load and the size of your cluster.
- **Management**: Centralized control makes it easier to manage configuration, updates, and policies from a single point.
