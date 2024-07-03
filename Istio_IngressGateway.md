If you are using Istio's ingress gateway, you typically do not need an external Application Load Balancer (ALB) to handle incoming traffic. Here’s how it works:

1. **Istio Ingress Gateway**: The Istio ingress gateway can be configured to handle all incoming traffic to the cluster. It acts as the entry point for external traffic and can be used to route requests to the appropriate services within the cluster.

2. **Load Balancer Integration**: Instead of an ALB, you can use a simpler external load balancer, such as a Network Load Balancer (NLB) or even a simple Layer 4 load balancer, to distribute traffic to the Istio ingress gateway. This load balancer does not need to handle application-level routing because Istio will take care of that.

![image](https://github.com/HimanshuMishra123/Kubernetes-Zero-to-Hero/assets/164254902/99f0fb28-0937-46b1-9129-dfe11a93700f)

![Istio Service Mesh on AWS EKS _ Step by Step Guide to install Istio Service Mesh on Kubernetes 0-1 screenshot](https://github.com/HimanshuMishra123/Kubernetes-Zero-to-Hero/assets/164254902/902a8e43-c162-45e9-9d6f-786fa8de935d)

### Benefits of Using Istio Ingress Gateway:

1. **Unified Traffic Management**: With Istio managing both external and internal traffic, you get a unified system for applying traffic management policies, security rules, and observability.

2. **Advanced Routing**: Istio provides advanced routing capabilities, such as path-based routing, header-based routing, and weighted load balancing, which can be configured at the ingress gateway.

3. **Security**: Istio’s ingress gateway can enforce mTLS and other security policies right from the point of entry, ensuring secure communication throughout the service mesh.

4. **Observability**: Istio provides detailed telemetry and tracing, giving you visibility into traffic entering and traversing the service mesh.

### Example Configuration:

Here’s a high-level example of how you might set this up:

1. **Deploy Istio Ingress Gateway**: Ensure that Istio is installed and the ingress gateway component is deployed.

2. **Create a Gateway Resource**: Define a Gateway resource in Kubernetes to configure the Istio ingress gateway. This resource specifies the ports, protocols, and other settings for incoming traffic.

3. **Define Virtual Services**: Create VirtualService resources to route traffic from the ingress gateway to specific services within the cluster.

4. **External Load Balancer**: Optionally, set up a simple external load balancer to direct traffic to the Istio ingress gateway’s IP address.

Here’s a simplified example of a Gateway and VirtualService configuration:

```yaml
# Gateway configuration
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: my-gateway
spec:
  selector:
    istio: ingressgateway # Use Istio's ingress gateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"

# VirtualService configuration
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
  - "*"
  gateways:
  - my-gateway
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: my-service.default.svc.cluster.local
        port:
          number: 80
```

In this setup:<br/>

- The external load balancer (e.g., NLB) directs traffic to the Istio ingress gateway. <br/>
- The Istio ingress gateway handles incoming requests and routes them according to the defined Gateway and VirtualService configurations.<br/>
- Istio manages all traffic routing, security, and observability from the point of entry.<br/>

By using Istio's ingress gateway, you leverage Istio's powerful features for managing external traffic, eliminating the need for an external ALB.

For more detailed understanding on resource configurations read below...
 https://istio.io/latest/docs/concepts/traffic-management/#gateways