Kubernetes 101 Workshop at Cisco building 7100-7 in Durham
2017-06-20
#k8sroadshow

Apprenda.com - seems to be leading the workshops
Apprenda seems to be a service vendor for Kubernetes
Offers incident or service contracts

Kismatic Enterprise Toolkit (KET)

Git repository for workshop notes and files
https://github.com/apprenda/kubernetes-workshop

Created Digital Ocean account using jgoodwin@metabolon.com
Added $10 in purchased credit plus $15 promotion code LOWENDBOX

created a digital ocean api token from the API tab of the dashboard.

git cloned the workshop repo from above

using the bash for windows, ran:
./provision64linux do create

cluster was created with these IPs (may not still be provisioned by the time you read this)

nodes:
etcd1      - public 138.197.151.90  private 10.137.48.87
master1    - public 138.197.160.208 private 10.137.48.200
worker1    - public 138.197.160.233 private 10.137.240.3
bootstrap1 - public 138.197.168.17  private 10.137.240.4

next ssh into the bootstrap vm using:
ssh root@138.197.168.17 -i ssh/cluster.pem

edit /ket/kismatic-cluster.yaml
change the ssh file path from:
/ket/ssh/cluster.pem
to
/ket/kubernetes-workshop/ssh/cluster.pem
run:
./kismatic install apply -f kismatic-cluster.yaml



(assume the ssh/cluster.pem was created by the provisioning app)

Slide deck showing a report comparing related technologies
kubernetes, mesos, docker, and cloud foundry
https://goo.gl/w4sUGs

windows container support in kubernetes and dashboard ui/ux is primarily apprenda

master = brain/scheduler
etcd = storage
worker = workloads go here

kublets = agents that talk to the runtime (docker,rkt)
https://goo.gl/FEKN43

note to self, look up rkt

control plane (master or etc) must be on linux
workers can be on either linux or windows as of version 1.5
	kubelet was redeveloped primarily due to networking model, expect good support in 1.7

Sasha asserted that kubernetes master is actually containerized, too.

Default scheduler supports namespaces to partition out rights/areas

Pods are sets of related containers which have the same lifecycle. They will share resources. Minimal unit of scaling/deployment

Pods talking to other pods do so without NAT layering

Pods can be assigned labels which can be used as selectors later for management

Controllers match current system to desired state

Storage spec is used to define references to storage classes to persist storage outside of pods.

Sasha mentioned there are some 30-odd storage classes already defined.

Pods are not the entrypoint to your microservices.
Service abstraction provides a clustered entrypoint to the pods underlying.
The service uses a layer3 load balancer.

Services are different from kubeproxy, but kubeproxy will route traffic to services.

Services are defined using label selectors. Service label spec must be equal or subset to the pods to which they match. (Pods can be more labeled, but not less than the service they match to)

Services should ideally be created before the Pods since it will assign the affinity to workers better. In a later version of Kubernetes (expected 1.6.4), this will be revised from the default behavior to be consistent regardless of order of service/pod creation.

popular plugins:
heapster - collects data from workers to give stats, good for dashboards

services can be defined to have rolling deployments where pods are created with the new version in increments, and after new version pods come up, old pods drop off.

By crossing the service selectors, you can cross over the traffic from new versions back to old version to handle cut-overs for incompatibility problems.

Ingress is the definition for how you define incoming traffic routing.
Can be used to route portions of traffic to one or another service for staged rollouts.

implemented as a reverse proxy (eg. nginx)

ingress has its own yaml config

services can be defined as a node port - can let you use the host to route traffic out - doesn't seem as useful as using the ingress rules

kubeadm is the community managed tool to 

lab using Kismatic Enterprise Toolkit (KET)
used by apprenda to script out the cluster setups

Process is as follows:
1) Setup control plane - typically multiple masters
1a) can put etcd on the same node, but typically on a different node
2) Add workers
3) Add networking
3a) Kubernetes requires products that support CNI (Container Networking Interface)
3b) Tools that use these apis can extend the dns to include their own dns rules


ReadinessProbe

In our yaml we can add a:
readinessProbe entry to define how a pod is known to be ready to receive work.


