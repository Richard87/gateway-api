# Testing Gateway API

```shell
# Setup Gateway API
k apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.0/experimental-install.yaml
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm install istiod istio/istiod -n istio-system --values values.yaml --version 1.27.1  --wait

# Install default gateway
k apply -f istio.yaml 

# Install demo app
k apply -f httpbin.yaml
```


Run a load balancer implementation, for example `cloud-provider-kind`:
```shell
go install sigs.k8s.io/cloud-provider-kind@latest
cloud-provider-kind
```



```shell
LB_IP=$(kubectl get svc/gateway-istio --namespace istio-system -o=jsonpath='{.status.loadBalancer.ingress[0].ip}')
curl http://${LB_IP} -H "Host: httpbin.example.com" -v
```