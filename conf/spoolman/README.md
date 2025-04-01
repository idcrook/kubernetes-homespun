# spoolman

See `spoolman-ingress-tls.yaml` for basic auth via `traefik`

<https://doc.traefik.io/traefik/middlewares/http/basicauth/>

## How to create basic auth


<https://stackoverflow.com/a/69915469>

```
printf "my-username:`openssl passwd -apr1`\n" >> spoolman-auth-secret
kubectl create secret generic spoolman-auth-secret --from-file spoolman-auth-secret
```


## NFS helper

```
cd /share/CACHEDEV1_DATA/nfs
/nfs/nfs_spoolman_data
```


## issue with starting uvcorn

```
Error: Invalid value for '--port': 'tcp://10.43.79.176:80' is not a valid integer.
```

https://github.com/Donkie/Spoolman/blob/ec5173067e2feb0009ee3409f9b004bb601d4530/entrypoint.sh#L17

```
# Execute the uvicorn command with any additional arguments
exec su-exec "app" uvicorn spoolman.main:app --host $SPOOLMAN_HOST --port $SPOOLMAN_PORT "$@"
```

### change name of service in kubernetes

`spoolman` -> `spoolman-svc`
