# Playground for all things Docker and Kubernetes

## Notes
### Containers with Docker
- Docker makes it easy to create, package and distribute applications in containers
- Docker resolved the dependency hell issue
- chroot - change root
- BSD - Barkely software distribution
- A jail is a lightweight virtualization technology with the BSD operating system
- Namespaces
    - user
    - pid - process id
    - network - isolated network stack
    - mount
    - uts - unix time sharing
    - ipc - inter process communication namespace
    - cgroups
        - Developement by google started in 2006
        - control groups
- **Virtual machines**
    - VMware ESXi
    - Hypervisor
        - allows multiple VMS to run ontop of it
- **Docker**
    - founded in 2010
    - shared kernel between containers
    - uses namespaces and cgroups
    - Docker extensions allow developers to bundle docker-based apps for easy installation
```bash
docker run -it ubuntu bash
```
runs the docker ubuntu image in an interactive input/output mode with a bash prompt
- **Container Images**
    - A container is a self contained bundle of software and dependencies that allow us to run software consistently across compute environments
    - a container image is a bundle of software
    - a container is an instance of the software that runs
    - A tag is used to distinguish a version of a container image
    - latest is a default tag and not necessarily the latest version of the image
    - A container image is a stack of individual layers on top of each others
    - Layers are filesystems
    - A digest is a unique identity for an image from the container registry, it is calculated as a hash of the image’s content, its layers and metadata using sha256
    - You can pull an image with its digest if you really want to be specific
    ```bash
    docker manifest inspect spurin/funbox
    ```
    Inspects the funbox image and shows its layers
    ```bash
    docker save spurin/funbox -o funbox.zip
    ```
    save the funbox image locally in a zip format
- **Running Containers**
    - docker uses containerd and runc to run containers
    - add `-d` to the docker run command to detach from the terminal and run the container in the background
- **Container Networking services and volumes**
    - use the `-P` command while running a container to publish all ports which allows the host machine get access to the container ports
    - use the `-p <preferred port>:<source port>` while running a container to specify your preferred port for the container in your local machine
    - you can execute another process in a running container using the `docker exec` command
    - use the `-v` command to add a full system path for a volume mount for the container. e.g:

    ```bash
    docker run -d --rm -p 12345:80 -v /Users/sheddy/Documents/Developer/docker-and-kubernetes-playground/test_page:/usr/share/nginx/html nginx
    ```
- **Building Container Images**
    - A dockerfile is a set of instructions responsible for defining or structuring a container image
    - when writing a dockerfile
        - `FROM` defines the parent image
        - `RUN` runs a command
        - `CMD` used to define a command that a container executes by default when a container is launched
        - `WORKDIR` sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow in the Dockerfile
    - to create multi arch image `docker buildx create --name buildx-multi-arch`
    - `docker system prune` to delete leftover image

## Kubernetes
- Kubernetes is a container orchestrator for running containers in mass
    - It is the successor of Google’s borg and omega projects
    - can scale from 1 to 1000s nodes
    - **Features**
        - Metrics collection
        - logging
        - autoscaling
        - self healing
- dockershim is the component that allows Kubernetes use Docker as a container runtime
- containerd is a high-level container runtime that manages the entire lifecycle of containers from image storage and distribution to container creation, start, stop, and deletion
- runc is a low level container runtime used by containerd and other popular container runtimes. It is responsible for setting up a container’s namespaces, cgroups, and other low-level linux primitives that are required for containers to run.
- cri-containerd
- Kubernetes pods are smallest compute units you can deploy and manage in Kubernetes. It is a group of containers
- **The Pause Container**
    - Every pod has a pause container
    - A pause container contains a shared namespace that the containers within the pods will use to run. It is the foundation for networking and sharing of all containers in that pod, it also controls the network namespace of the pod.
    - IPC means inter-process communication
    - Create a pause container with a shareable IPC - `docker run --rm -d --ipc=shareable --name pause -p 3030:80 registry.k8s.io/pause:3.9
    - Run MYSQL as a container within the pause container namespace - `docker run --rm -d --name mysql -e MYSQL_DATABASE=exampledb -e MYSQL_USER=exampleuser -e MYSQL_PASSWORD=examplepass -e MYSQL_EANDOM_ROOT_PASSWORD=1 --net=container:pause --pid=container:pause mysql:8`
    - Run Wordpress with mysql within the pause container shared namespace - `docker run --rm -d --name wordpress -e WORDPRESS_DB_HOST="127.0.0.1" -e WORDPRESS_DB_USER=exampleuser -e WORDPRESS_DB_PASSWORD=examplepass -e WORDPRESS_DB_NAME=exampledb --net=container:pause --ipc=container:pause --pid=container:pause wordpress:6.1.1`
    - processes can make use of shared memory to share resources, this is all thanks to IPC
- The primary function of a pod in Kubernetes is to provide an environment for one or more containers to share
- What happens when a failure occurs within a container in Kubernetes?
	- when a pod is created, it'll be created with a `resartPolicy` as always by default. This means that Kubernetes will always restart a pod if failure occurs
	- you can alter the restart policy when creating the pod with --restart=<restart-policy>
- kubectl create will validate a resource before applying it
- kubectl apply does not validate a resource before applying it
- A Kubernetes deployment is a declarative means for defining a desired state. You can use deployments to specify how an application should run as well as the number of replicas that should be running. Deployments also provide useful interaction points for other areas in K8s such as Services.
- When a deployment is created, a replicaset is also created for the deployment with a replicaset id appended to the replicaset name
- A Repilicaset in Kubernetes is a controller that ensures that a specified number of replica pods are running and available at all times. It creates and manages a set of identical pod replicas and is responsible for scaling the number of replicas up or down based on the desired state specified in the Kubernetes configuration
- Kubernetes services are a way to expose your application running on a set of pods as a network service. Services provide a stable endpoint for external clients to access the application, as well as load balancing and service discovery
	- **Cluster IP service**: This is the default type of service and it creates a virtual IP that is onl accessible within the cluster. This type of service is useful for communication between different parts of your application that are running in the cluster.
	- **NodePort service**: This type of service exposes a service on a specific port on each node in the cluster. This allows external traffic to access the service by connecting to any of the node IP and the specified port
	- **LoadBalancer service**: This type of service create a load balancer in a cloud provider's infrastructure and forwards traffic to the service. it is useful for exposing your application to external clients
	- **ExternalName service**: This type of service creates a CNAME record that maps a service to an external DNS name. This is useful for connecting to services that are not in your cluster, such as a database running in a different cluster or a different cloud provider
	- **Headless service**: This type of service is similar to a ClusterIP service but does not create a virtual IP. Instead, it allows you to directly access individual pods via DNS. It is useful for scenarios where you need direct communication between pods and/or fine-grained control over load balancing
- **Kubernetes DNS**
	- Kubernetes is provided with a built in DNS service that automatically assigns DNS names to Services and Pods in a cluster
	- Kubernetes Service DNS format -> `<servicename>.<namespace>.svc.cluster.local`
	- Kubernetes Pod DNS format -> `<podname>.<namespace>.pod.cluster.local`
		- The pod name would be the pod IP
- **Kubernetes Architecture**
	- a daemonset is similar to a deployment but it'll run on all nodes in a cluster
	- **Kubelet**
		- A kubelet is the primary 'node agent' that runs on each node
		- The kubelet is often referred to as the 'worker' and it ensures that containers are running in a pod.
		- The kubelet is a core component of Kubernetes that runs on each worker node, ensuring seamless communication between the control plane and node components. It manages the pod lifecycle by watching for pod specifications from the API server, creating, starting, and stopping containers as needed.
		- Kubelet can also create static pods, which are defined directly on the node and not managed by the API server, providing a means for running system-level services even if the control plane is unavailable
	- **etcd**
		- etcd is a consistent and highly available key value store, used as the Kubernetes backing store for all cluster data
		- use `etcdctl` for interacting with etcd in a cluster
		- Capture the ENDPOINT as a variable -
			```bash
			ENDPOINT=$(ps -ef | grep etcd | awk -F ' ' '{for(i=1;i<=NF;i++){if ($i ~ /--advertise-client-urls=/) {print $i}}}' | awk -F= '{print $2}');echo $ENDPOINT
			 ```
		- Using the ENDPOINT variable, query etcd using the etcd-client cli, prefix the command with ETCDCTL_API=3 to use v3 of the API -
			``` bash
			ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key --write-out=table endpoint status
		- use etcd to query the available keys that are being used by Kubernetes -
			```bash
			ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key get / --prefix --keys-only | grep -v ^$ | head -10
	- **Kube API server**
		- This is the main point of interaction for kubectl
		- It validates and configures data for API objects including pods, services, replication controllers and more
		- the `.kube/config` file is responsible for guiding kubectl on how to interact with the kube-apiserver
	- **Kube scheduler**
		- The kube scheduler is a control plane process that assigns pods to nodes (kubelets)
		- it determines which nodes are valid according to constraints and available resources
		- it ranks each valid node and binds the Pod to the most suitable one
	- **Controller Manager**
		- Controller manager is a daemon that embeds the core control loops shipped with Kubernetes
		- A control loop is a non-terminating loop that regulates the state of the system
		- It monitors the state of the cluster through the kube-apiserver and makes changes accordingly to change the state to the desired state
	- **kube proxy**
		- Kubernetes network proxy provides simple TCP, UDP and SCTP stream forwarding, round-robin TCP, UDP and SCTP forwarding across a set of backends
	- **CoreDNS/KubeDNS**
		- CoreDNS is a golang based DNS server that stores DNS records and answers domain name queries
		- kube-dns is the name of the service that serves on 53 for TCP/UDP ( standard DNS ), this points at the coredns pods
		- Historically, kube-dns was a DNS server in Kubernetes <= v1.11
		- The service name of kube-dns was by design, kept for compatibility purposes
