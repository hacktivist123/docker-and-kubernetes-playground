# Linkding On Kubernetes

This repo demostrates how to run [linkding](https://linkding.link/) on a Kubernetes cluster. 

This setup uses a `NodePort` service to expose the linkding pod running on my k3s cluster in my homelab. The reason for using a `NodePort` service is to expose it to every machine running on my tailscale network. This way, i do not have to portforward the pod which is bound to localhost.
