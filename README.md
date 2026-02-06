# PortfolioKubernetes

# Metallb
# Check your Docker network
```bash
docker network inspect bridge | findstr -i "subnet"
```

# Openbao
```markdown
# PortfolioKubernetes

## OpenBao — Essential Commands (with why)

1) Initialize OpenBao and produce unseal keys (saves JSON locally as `keys.json`)

```bash
kubectl exec -n openbao -it <openbao-pod> -- openbao operator init -key-shares=5 -key-threshold=3 -format=json > keys.json
```

Why: Initializes the OpenBao cluster and generates the unseal key shares and root token; `keys.json` contains the key shares needed to unseal the server.

2) Store the generated `keys.json` as a Kubernetes Secret named `openbao-unseal-keys`

```bash
kubectl create secret generic openbao-unseal-keys --from-file=keys.json=./keys.json -n openbao
```

Why: Preserves the unseal keys inside the cluster so init containers or Jobs can read them to perform automated unseal; keeps the keys out of shell history and local files once stored.

3) Use the stored keys to unseal OpenBao (example: a pod-local curl loop or Job that posts keys to the HTTP unseal endpoint)

```bash
# Example: run inside the OpenBao pod or a privileged helper pod
for k in $(jq -r '.keys[]' /path/to/keys.json); do
  curl -s -X PUT -d '{"key":"'"$k"'"}' http://127.0.0.1:8200/v1/sys/unseal || true
done
```

Why: Submits each key share to the OpenBao HTTP API until the unseal threshold is met, moving the server from sealed to unsealed state so it can serve requests.

```