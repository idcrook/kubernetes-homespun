# performed on each node host

## traefik

on the designated traefik host

```
# traefik lets encrypt
sudo mkdir -p /srv/configs/acme/
sudo touch /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme.json
```

## NFS

this seems to already be available on Ubuntu 18.04 server

```
sudo apt install -y nfs-common
```
