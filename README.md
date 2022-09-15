<img src="icons/istio
.png" />

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

you must  explicity enable Istio Sidecar injection at a namespace level if you would like istio to inject proxy services as sidecars to the applciations deployed in a namespace

`kubectl label namespace <MY-NAMESPACE> istio-injection=enabled`

> you can check the status of the mesh with the following command. `istioctl analyze`

## Kiali

Kiali is an observability console for Istio with service mesh configuration and validation capabilities. It helps you understand the structure and health of your service mesh by monitoring traffic flow to infer the topology and report errors. 

### [install Kiali](https://kiali.io/docs/installation/quick-start/)

`kubectl apply -f ${ISTIO_HOME}/samples/addons/kiali.yaml`