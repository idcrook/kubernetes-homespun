

## traefik

```
git submodule add -b master https://github.com/containous/traefik.git
git commit -a -m "added traefik"
```

```
# traefik lets encrypt
sudo mkdir -p /srv/configs/acme/
sudo touch /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme.json
```

## NFS

```
sudo apt install -y nfs-common
```
