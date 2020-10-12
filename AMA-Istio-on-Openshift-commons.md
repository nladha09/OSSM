#### AMA on Istio

##### Useful Techniques for making sense of Istio Service Mesh

Daniel Berg & Lin Sun - Istio Explained (getting started with Service Mesh) Co-founders of Istio w/ Google

- Managing microservices doesn't need to be complicated
- What is a service mesh - A service mesh is programmable framework that allows you to observe, secure, & connect microservices. 
  - (modern day programmable mechanism to manage / connect microservices)
- Istio is an implementation of a service mesh (one of the very first)
- Istio architecture (data plane & control plane)
  - control plane - where framework is actually programmed - istiod (a single binary - easier to manage)
  - data plane - a set of sidecar proxies injected into services ( how they are to communicate)
  - istio gateway (takes inbound traffic / access - can support mTLS...etc.) (istio egress gateway - define policies for connection on external / exit points)
  - Istio Techniques
    - simplified setup & management (install via CLI w/ config profiles) `istioctl` (istio-cuttle)
    - IBM offers a managed service of Istio - "Managed IStio" for a one click install
  - Introduce our application (https://github.com/istio-explained)
  - deploy the application (deployed db2 statefulset in stock-trader-data)
  - Securing inbound traffic (connect istio ingress gateway w/ IBM cloud NLB)
  - Simplify adding services to the mesh - existing services / new service / confirm pods are running w/ sidecars
  - Observe traffic communication with no change
  - Observe trace spans for each request (Jaeger dashboard)
     - click on a request to view trace spans amoung services
  - Securing communication withi Istio (permissive mTLS (enabled by default) improves mTLS onboarding experience) if if doesn't wprk it will fall back to continue working
    - enforce mTLS traffic in the mesh w/ authentication policy (introduced in v1.5)
  - Control Traffic
  - Debugging `istioctl analyze` or `istio describe` (analyze istio resources - yaml file, namespace, entire cluster...etc.)
    - check proxy config - `istioctl proxy-config route istio-ingressgateway-5d6c9f74cb)
    - check proxy status - `istioctl proxy-status`
  - what's new in the comm - Quarterly releases
  - istio slack - connect
