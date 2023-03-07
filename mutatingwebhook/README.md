# Mutating Webhook example

## Setup the webhook

```
kubectl apply -f webhook.yaml
```

## Set up the CA Certificate
Once the webhook runs (give it a few seconds to initialize), the CA certificate can be downloaded by executing a curl command within the container. To retrieve the base64 encoded version of this ca.pem, use the following command:
```
kubectl exec -it -n mutatingwebhook $(kubectl get pods --no-headers -o custom-columns=":metadata.name" -n mutatingwebhook) -- wget -q -O- localhost:8080/ca.pem?base64
```

The output of this command should replace the base64 string in caBundle in webhook.yaml:
```
    caBundle: "cGxhY2Vob2xkZXIK" # <= replace this string within quotes
```

Then reapply the webhook using:
```
kubectl apply -f webhook.yaml
```
Start the Pod
```
kubectl apply -f pod.yaml
```
get the pod
```
kubectl get pods -A
```
The output of this command will be like
```
testmutatingwebhook   ubuntu                             1/1     Running   0               20s
```
To vaidate the webhook in a json file apply the command 
```
kubectl get pods -n testmutatingwebhook ubuntu -o json
```
The output shoud be like
```
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
        "annotations": {
            "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"ubuntu\"},\"name\":\"ubuntu\",\"namespace\":\"testmutatingwebhook\"},\"spec\":{\"containers\":[{\"command\":[\"/bin/sleep\",\"1d\"],\"image\":\"ubuntu:latest\",\"imagePullPolicy\":\"IfNotPresent\",\"name\":\"ubuntu\"}]}}\n"
        },
        "creationTimestamp": "2023-03-07T08:32:58Z",
        "labels": {
            "app": "ubuntu",
            "myExtraLabel": "webhook-was-here"
        },
        "name": "ubuntu",

