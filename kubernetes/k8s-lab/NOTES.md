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

- in kubernetes, the filesystem is ephemeral. When a pod is deleted, the filesystem is deleted with it.

- The primary reason for using an ephemeral volume is to share data between containers in a pod.
