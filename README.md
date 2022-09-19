<img src="icons/istio.png" />

# Istio

## Service Mesh

It is a dedicated and configurable infrastructure layer that handles the communication between services without having to change the code in a microservice architecture.

## Envoy

The proxy used by Istio is [Envoy](https://www.envoyproxy.io/) and it's located in the same POD of our application as a sidecar, along this, there's another component called istio agent

## Control Plane (istiod)

* Citadel: Certificate generation
* Pilot: Service Discovery
* Galley: Help in validating configuration files

## Microservices Cross Cutting Concerns 

* Authentication
* Authorization
* Networking
* Logging
* Monitoring
* Tracing

> Microservices cons: 
>* Networking
>* Security
>* Observability
>* Overload for traditional operation Models

## Install Istio

curl -L https://istio.io/downloadIstio | sh -
cd istio-<version-number>
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y

## Enable Istio Sidecar

you must  explicity enable Istio Sidecar injection at a namespace level if you would like istio to inject proxy services as sidecars to the applciations deployed in a namespace.

`kubectl label namespace <MY-NAMESPACE> istio-injection=enabled`

> you can check the status of the mesh with the following command. `istioctl analyze`

## Trafic Management

### Istio Gateways

Istio gateway describes a load balancer operating at the edge of the mesh receiving incoming or outgoing HTTP/TCP connections.

Istio supports Kubernetes ingress but there is also another approach that Istio offers and recommends in order to have more feature such advanced monitoring and routing rules, this is called Istio Gateway.

<img src="icons/istio-gateway.png" />

Istio deploys ingress gateways using Envoy proxies, all the services have an Envoy proxy deployed as a sidecard contianer, however, the ingress and egress gateways are just standalone Envoy proxies, sitting at the edge of the service mesh (the do not work as a sicard).

List the created wateways
- `kubectl get gateway`
- `kubectl get gw`

View details of an particular gateway
- kubectl describe gateway <GATEWAY_NAME>
  - `kubectl describe gateway my-gateway`

### Virtaul Services

- Virtual services define a set of routing rules for traffic coming from ingress gateway into the service mesh.
- All routin rules are configured through virtual services.
- Define a set of routing rules for traffic comming from ingress gateway.
- VS are flexible and powerfull.
- When a virtual service is created Istio control plane applies the new configuration to all the Envoy sidecar proxies.
- you can specify many rules, for example:
  - the routing percent for each service in the virtual service configuration file [(Example)](03-virtual-service-configurations/01-weight.yaml).
  - send traffic to a specific service according to the user logged in [(Example)](03-virtual-service-configurations/02-match-header.yaml).
- Fault Tolerance: Introduce some errors to check if errors hanldling mechanisms are working as expected [(Delay Example)](03-virtual-service-configurations/03-fault-tolerance-delay.yaml) [(Abort Example)](03-virtual-service-configurations/04-fault-tolerance-abort.yaml).
- Timeouts: if a service is taking to much time to respond it must no keep the dependent service waiting forever, it must fail after a period of time and return an error message [(Example)](03-virtual-service-configurations/05-timeout.yaml).
- Retries: Attempt the operation again [(Example)](03-virtual-service-configurations/06-retries.yaml), Istio by default has the following configuration:
  - 25 ms+ intervals after 1st fail.
  - 2 retries before returning an error.

List the created virtual services
- `kubectl get virtualservice`
- `kubectl get vs`

### Destination Rules

- Destination rule defines policies that apply to traffic intended for a service after routing has occurred so with a destination rule you can add additional routing policies on top of a kubernetes services.
- The destination rules object creates subsets that are used in the virtual service object.
- Allows us to have different load balancer policies such as:
  - ROUND_ROBIN  (Default)
  - LEAST_CONN
  - RANDOM
  - PASSTHROUGH
- Circuit breaking: Mark the requests failed immediately instead of send them to the failing service [Example](04-destination-rule-configurations/01-circuit-breaking.yaml).

List the created destination rules
- `kubectl get destinationrules`
- `kubectl get dr`

## Security

- Istio security architecture: has security checks in every point not just the entry point to the network and this is called security at depth 
- Authentication:  In service oriented architecture we need to make sure that communication between services are authenticated, meaning when a service tries to communicate with another service there should be a way to confirm they're really who they say they are (Mutual TLS / JWT validation).
  - Istio provides a PeerAuthentication object in order to have authentication in an specific worload, namespace-wide or mesh-wide policy [Mesh-Wide Example](05-security/01-peer-authentication.yaml).
- Authorization: We can control which service can reach which service, Istio provides authorization mechanisms that allow us to define policies to allow or deny requests based on certain criteria [Example](05-security/02-authorization-policy.yaml).
- Certification Management: When a service is started, it needs to identify itself to the mesh control plane and retrieve a certificate in order to serve traffic. Istiod has a built-in certificate authority and creates its own certificates, however, you can use your own certificates.

## Observability

### Visualizing Metrics (Prometheus and Grafana)

The standard Istio metrics are exported to Prometheus by default, then, as an Istio add-on your metrics can be monitored in grafana.

### Distributed Tracing (Jaeger)

It's a way to monitor individual requests in order to understand behavior of services

### Observability Console (Kiali)

Kiali is an observability console for Istio with service mesh configuration and validation capabilities. It helps you understand the structure and health of your service mesh by monitoring traffic flow to infer the topology and report errors. 

### [install Kiali](https://kiali.io/docs/installation/quick-start/)

`kubectl apply -f ${ISTIO_HOME}/samples/addons/kiali.yaml`