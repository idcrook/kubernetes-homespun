# to set up Basic Auth 

see the example to create `traefik-auth-cm.yaml`

```
# htpasswd -nb user password 


---
apiVersion: v1
kind: ConfigMap
metadata:
  name: traefik-htpasswd  

data:
  htpasswd.txt: |
    user:$apr1$hULWH8YR$2TDc..8Ufi4en/NnKkjB50
    user2:$apr1$UWZDNP1j$VE5O8Z5zah.oT2frEXQKO.

```

# for tinyauth


```shell
# for secret - currently needs to be exactly 32 characters long
echo -n 'your-generated-secret' | base64

# for each username
docker run -i -t --rm ghcr.io/steveiliop56/tinyauth:v3 user create --interactive
```
