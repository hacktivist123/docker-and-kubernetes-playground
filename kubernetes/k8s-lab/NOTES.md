# Research Notes

- A Deployment provides declarative updates for Pods and ReplicaSets.

  - You describe your desired state in a Deployment, and the Deployment Controller's job is to make the current state match the desired state. You declare your hopes and dreams, and it's Kubernetes' job to make them come true.

- A ReplicaSet maintains a stable set of replica Pods running at any given time. It's the thing that makes sure that the number of Pods you want running is the same as the number of Pods that are actually running.

- There are several ways to manage environment variables in Kubernetes. One of the most common ways is to use ConfigMaps. ConfigMaps allow us to decouple our configurations from our container images, which is important because we don't want to have to rebuild our images every time we want to change a configuration value.

- ConfigMaps are insecure

  - ConfigMaps are a great way to manage innocent environment variables in Kubernetes. Things like:

    - Ports
    - URLs of other services
    - Feature flags
    - Settings that change between environments, like DEBUG mode

- Services provide a stable endpoint for pods. They are an abstraction used to provide a stable endpoint and load balance traffic across a group of Pods. A service will always be available at a given URL, even if the pod is destroyed and recreated.

- The ingress object not only exposes your service to the outside world, but also allows you to do things like:

  - Host multiple services on the same IP address
  - Host multiple services on the same port (path-based routing)
  - Terminate SSL
  - Integrate directly with external DNS and load balancers

- An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet typically uses a service of type `Service.Type=NodePort` or `Service.Type=LoadBalancer`.

- In production, once you have an ingress configured and have pointed your domain name to it (and perhaps its load balancer), you can access your application from anywhere in the world.

- By default, containers running in pods on Kubernetes have access to the filesystem

- In kubernetes, the filesystem is ephemeral. When a pod is deleted, the filesystem is deleted with it.

- The primary reason for using an ephemeral volume is to share data between containers in a pod.

- Instead of simply adding a volume to a deployment, a persistent volume is a cluster-level resource that is created separately from the pod and then attached to the pod. It's similar to a ConfigMap in that way.

- Persistent Volumes can be created statically or dynamically.

 - Static PVs are created manually by a cluster admin
 - Dynamic PVs are created automatically when a pod requests a volume that doesn't exist yet

- A persistent volume claim is a request for a persistent volume. When using dynamic provisioning, a PVC will automatically create a PV if one doesn't exist that matches the claim.

- `kubectl top pod` command  will show you the resources that each pod is using.

- There are two ways to scale an application: vertically and horizontally.

- We can set resource limits in our deployment files

- A Horizontal Pod Autoscaler can automatically scale the number of Pods in a Deployment based on observed CPU utilization or other custom metrics. It's very common in a Kubernetes environment to have a low number of pods in a deployment, and then scale up the number of pods automatically as CPU usage increases.

- there are two types of machines in a production Kubernetes cluster:

  - Control Plane
  - Worker Nodes

- A resource request is the amount of a resource that a pod requests from the node it's running on. A resource limit, on the other hand, is the maximum amount of a resource that a pod is allowed to consume before it's throttled or killed.
  - One of the most important things to get right when working with pod autoscalers in Kubernetes are the resource requests and limits. If you don't set them correctly, you can end up with a situation where your pods are crashing, or your autoscaler is scaling up too many pods.

   - Set memory requests ~10% higher than the average memory usage of your pods
   - Set CPU requests to 50% of the average CPU usage of your pods
   - Set memory limits ~100% higher than the average memory usage of your pods
   - Set CPU limits ~100% higher than the average CPU usage of your pods
   

 
