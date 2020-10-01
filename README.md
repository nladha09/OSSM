# OSSM
---
##### Training material
#
- 1.) https://start.learning.redhat.com/catalog/learning-path/214
- 2.) https://www.istiobyexample.dev/

---

##### Helpful tools
#


---

##### Notes / important threads
#

interesting thread w istio-support on privileges in grafana/jaeger UI; commands shared, specifically `oc policy` and `oc auth`. ive not used those commands w any regularity and they are not necessarily restricted to OSSM: RBAC roles restricting users to view-only access in OSSM jaeger/grafana UIs (tkt#**02750453**)

- **Question**: do we have any documentation or guidance in creating roles for view-only access to the above? my guess is that it follows vanilla k8s, but in particular, what are the cluster resources involved? the RBAC verbs, im supposing to be used are *list/watch/get*.

- **Answer**: Yes, it follows k8s directly.  Grafana, Kiali, and Jaeger all delegate through ouath proxy. The simplest is probably to give users "view" permissions on a project.  This can be done by the project admin (i.e. doesn't have to be cluster admin).  I think the command is:

`oc policy add-role-to-user -n myproject joeuser view`

but you should probably verify.

That does not sound correct.  I would use `oc policy who-can` ... to verify what the user can do.  What do you mean by "admin" access, as neither of those applications allow modification of any installed applications or the control plane.
#
- **Question**: Please find the grafana screen shot which i logged in with User(mani) has only view in istio-system(controlplane.). This is what i meant in grafana UI, user was getting admin(role) access after we add the user as view in istio-system namespace. On Namespace User only has view access.

```
[quicklab@upi-0 ~]$  oc auth can-i get pods -n istio-system --as=mani
yes
[quicklab@upi-0 ~]$  oc auth can-i edit pods -n istio-system --as=mani
no
[quicklab@upi-0 ~]$  oc auth can-i edit route -n istio-system --as=mani
no
[quicklab@upi-0 ~]$  oc auth can-i get route -n istio-system --as=mani
yes
```

- **Answer**: This is something that should probably be fixed.  That said, all users are "admin" in grafana, which I think just means they can add dashboards, etc.  Please create a JIRA.  (I noticed that the default openshift grafana doesn't allow the user to do anything, even cluster-admin, so that could serve as an example for how to lock this down.)

---

Service Mesh & Envoy - Christian Posta https://openshift.tv 
https://www.manning.com/books/istio-in-action (will be done in spring)
- why are there so many service mesh(es)? - people have diff opinions on what networking abstractions should look like (user exp., managability ...etc. just like containers)
  - large majority are based on envoy (underneath the hood) CNCF / open source - (written in C++) 
  - minimum viable SM - 95% ingress gateway (getting traffic into their cluster)
  - traffic b/w services to be encrypted (security) first use case for users
  - what we're saying is instead of having centralized proxies - why don't we create a small, lightweight, compact one for each app... (no centralized proxy)
    - b/w apps if traffic is going to another app (encrypting traffic)
  - let's give envoy these certs & encrypt is before it leaves app & before it gets to app (direct end-to-end encryption) keys on both sides = mTLS
    - control plane level Istio / AWS App Mesh (proxies deploy app & control plane to drive config)
  - in istio - the certs are stored in memory (those certs get rotated after a certain amount of time)
  - what do you think is after kubernetes (Service Mesh!)
    - 1.) k8s & that kind of deployment platform is amazing at taking a container (app) & deploy it across multiple nodes (large fleet) & make sure there's a certain number of replicas, check their health - container orchestrator, but when you deploy these apps, they need to talk to each other (network layer b/w apps).
    - 2.) just like k8s solved the problem of deploying & managing - how do we solve the communication (regardless of language / framework) challenges? & keep it up-to-date & correctly implemented (becomes operationally difficult & complex) in an operational & framework agnostic way.
  - then you look at ways of optimizing that - the infra you built to get there (those are necessary pieces of functionality)
  - webassembly module (run in browser / envoy) take code written in preferred language (with some contraints) & compile to a binary format & run it in a VM or sandboxed env.
    - capabilities are limities (no shared memory - copy b/w VM & host; external services have to be made externally / explicitly)
    - to extend the control plane components / kubernetes itself
