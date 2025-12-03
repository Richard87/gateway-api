# Testing Gateway API

```shell
kind create cluster --name istio
alias k="kubectl"

# Setup Gateway API
k apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.0/experimental-install.yaml --server-side

# Install Istio
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm install istiod istio/istiod -n istio-system --values values.yaml --version 1.28.0 --create-namespace  --wait
# helm upgrade istiod istio/istiod -n istio-system --version 1.27.1 --values values.yaml

# Install demo app
k apply -f httpbin.yaml

# Install default gateway
k apply -f network.yaml 
```


Run a load balancer implementation, for example `cloud-provider-kind`:
```shell
go install sigs.k8s.io/cloud-provider-kind@latest
cloud-provider-kind
```



```shell
export LB_IP=$(kubectl get svc/gateway-istio --namespace istio-system -o=jsonpath='{.status.loadBalancer.ingress[0].ip}') && echo $LB_IP
curl -k https://httpbin.example.com/json --resolve httpbin.example.com:443:${LB_IP} -v
curl http://httpbin.example.com/json --resolve httpbin.example.com:80:${LB_IP} -v
```