kubernetes-homespun
===================

Build a kubernetes cluster in home network (serving externally)

-	Raspberry Pi-s
-	kubernetes v1.17.3 via k3s v1.17.3+k3s1
	-	"external" NFS for persistent storage
	-	"external" postgresql database
-	[traefik](https://github.com/containous/traefik) v2.x for ingress
	-	includes Let's Encrypt (TLS certificates)

Hardware:

-	4GB Raspberry Pi 4 B : ubuntu 19.10 (arm64) : k3s worker node
-	Raspberry Pi 3 B+ : raspbian buster : k3s control plane node
-	Raspberry Pi 3 B : raspbian buster : postgresql DB (USB thumb drive for db storage)

Apps deployed:

-	[phant](https://hub.docker.com/r/dpcrook/phant_server-docker) - IoT data logging
-	[lighttpd](https://hub.docker.com/r/dpcrook/alpine-lighttpd-static) - Static webpage server
-	[miniflux](https://hub.docker.com/r/miniflux/miniflux) - RSS Feed aggregator and syncing
	-	uses postgres db

Get it done
-----------

-	[RUN.md](RUN.md) - Start kubernetes by installing `k3s`.

	-	`traefik` is ingress router
	-	Configure the deployments and services

-	[Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](.archive/CLUSTER.md) - old kubernetes cluster setup; uses `kubeadm`
