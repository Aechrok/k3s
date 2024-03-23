# Sealed Secrets

## Generate custom certificates and test secret

```bash
openssl req -x509 -days 7300 -nodes -newkey rsa:4096 -keyout ss.key -out ss.crt -subj "/CN=sealed-secret/O=sealed-secret"
kubectl -n kube-system create secret tls sealed-secrets-controller-custom-certs --cert=ss.crt --key=ss.key --dry-run -oyaml

## encrypt secret with a specific cert pair (values-dev has the static crt file you need for this command)
echo '
apiVersion: v1
data:
  USERNAME: YmxhaA==
  PASSWORD: YmxhaA==
kind: Secret
metadata:
  name: test
  namespace: test
type: Opaque' | kubeseal -n test --cert ss.crt --scope cluster-wide -o yaml -
```