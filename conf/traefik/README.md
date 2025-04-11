# to set up Basic Auth 

see the emaple to create `traefik-auth-cm.yaml`

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

