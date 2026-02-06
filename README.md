# PortfolioKubernetes

# Metallb
# Check your Docker network
```bash
docker network inspect bridge | findstr -i "subnet"
```
# Openbao
```bash
kubectl create secret generic openbao-unseal-keys --from-literal=keys.json='{"keys":["key1","key2","key3"]}' -n openbao
```

```bash
kubectl exec -ti openbao-0 -n openbao -- bao operator init
```