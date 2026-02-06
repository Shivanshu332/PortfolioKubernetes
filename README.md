# PortfolioKubernetes

# Metallb
# Check your Docker network
docker network inspect bridge | findstr -i "subnet"

# Openbao
kubectl create secret generic openbao-unseal-keys \
  --from-literal=keys.json='{"keys":["key1","key2","key3"]}' \
  -n openbao

kubectl exec -ti openbao-0 -- bao operator init