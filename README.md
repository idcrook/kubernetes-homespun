An in-home kubernetes cluster network (serves to Internet)

Apps and services deployed via kubernetes:
------------------------------------------

-	[phant](https://hub.docker.com/r/dpcrook/phant_server-docker) - IoT data logging
	-	https://data.crookster.org
-	[lighttpd](https://hub.docker.com/r/dpcrook/alpine-lighttpd-static) - Static webpage server
	-	https://www.crookster.org
	-	https://party.crookster.org
-	[miniflux](https://hub.docker.com/r/miniflux/miniflux) - RSS Feed aggregator and syncing
	-	https://miniflux.crookster.org

High-level
----------

-	Raspberry Pi-s
-	kubernetes via [k3s](https://k3s.io)
	-	"external" NFS server for persistent storage
	-	"external" postgresql server for database
-	[traefik](https://github.com/containous/traefik) v2.x for ingress
	-	includes Let's Encrypt (TLS certificates) and wildcard DNS support

### Hardware:

-	Raspberry Pi 4 B (4GB): Raspberry Pi OS (bookworm) : k3s control plane node
-	Raspberry Pi 4 B (4GB): Raspberry Pi OS (arm64 bullseye) : k3s worker node
	-	"external" postgresql DB, **USB thumb drive** for db storage
-	NAS : "external" NFS server
-	Raspberry Pi 4 B (4GB): Running BirdNET.Pi on PoE connection

#### Use "external" `postgresql` db

-	miniflux
-	freshrss

### Offline services (there may be others)

-	[Bird Net Pi](https://github.com/mcguirepr89/BirdNET-Pi) - A realtime acoustic bird classification system
	-	https://birdnetpi.idcrook.dev

---

Get it done
-----------

-	[BUILD.md](BUILD.md), [RUN.md](RUN.md) - Start kubernetes by installing `k3s`...

-	circa 2018 [Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](.archive/CLUSTER.md) - previous kubernetes cluster setup used `kubeadm`
