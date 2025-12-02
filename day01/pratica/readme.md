kind create cluster --config kind-cluster.yml --name giropops
kubectl run --image nginx --port 80 giropops
k expose pods giropops --type NodePort
k delete pods giropops
k delete svc giropops
kubectl run --image nginx --port 80 giropops --dry-run=client
kubectl run --image nginx --port 80 giropops --dry-run=client -o yaml
kubectl delete -f pod.yaml
kubectl delete service nginx