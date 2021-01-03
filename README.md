kubernetes-homespun
===================

A kubernetes cluster in home network (serves to Internet)

-	Raspberry Pi-s
-	kubernetes via [k3s](https://k3s.io)
	-	"external" NFS server for persistent storage
	-	"external" postgresql server for database
-	[traefik](https://github.com/containous/traefik) v2.3 for ingress
	-	includes Let's Encrypt (TLS certificates) and wildcard DNS support

Hardware:

-	4GB Raspberry Pi 4 B : Ubuntu 20.04 (arm64) : k3s worker node
-	Raspberry Pi 3 B+ : Raspbian Buster : k3s control plane node
-	Raspberry Pi 3 B : Raspbian Buster : postgresql DB
	-	USB thumb drive for db storage

Apps and services deployed via kubernetes:

-	[phant](https://hub.docker.com/r/dpcrook/phant_server-docker) - IoT data logging
-	[lighttpd](https://hub.docker.com/r/dpcrook/alpine-lighttpd-static) - Static webpage server
-	[miniflux](https://hub.docker.com/r/miniflux/miniflux) - RSS Feed aggregator and syncing
	-	uses "external" `postgresql` db
-	[hedgedoc](https://github.com/hedgedoc/container) - markdown writing and sharing
	-	uses "external" `postgresql` db

Get it done
-----------

-	[RUN.md](RUN.md) - Start kubernetes by installing `k3s`.

	-	`traefik` is ingress router
	-	Deployments and services have configuration files

-	[Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](.archive/CLUSTER.md) - old kubernetes cluster setup; used `kubeadm`
