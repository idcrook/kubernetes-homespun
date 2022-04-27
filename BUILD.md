performed on each node host
===========================

traefik
-------

on the designated traefik host

```
# traefik lets encrypt
sudo mkdir -p /srv/configs/acme/
sudo touch /srv/configs/acme/acme.json
sudo touch /srv/configs/acme/acme-wc.json
sudo chmod 600 /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme-wc.json
ls -la /srv/configs/acme/
```

NFS
---

this seems to already be available on Ubuntu 18.04 server

this seems to already be available on Raspbian `buster`

```
sudo apt install -y nfs-common
```

copying secrets
---------------

on donor

```shell
cd ~/projects/kubernetes-homespun/conf

scp traefik/traefik-envariable-secrets.yaml r64-01:projects/kubernetes-homespun/conf/traefik/
scp miniflux/miniflux-secrets.yaml          r64-01:projects/kubernetes-homespun/conf/miniflux/
scp wikijs/wikijs-secrets.yaml              r64-01:projects/kubernetes-homespun/conf/wikijs/
```
