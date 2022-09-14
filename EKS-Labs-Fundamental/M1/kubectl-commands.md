# Use Cluster
kubectl config view
kubectl config use-context <Replace-with-AWS-IAM-Username>@Dev.us-west-2.eksctl.io

# List all API Resources for Cluster
kubectl api-resources

# Create Namespace
kubectl create namespace mynamespace

# Get Namespace
kubectl get namespace

# Create Deployment
kubectl apply -f nginx-deployment.yaml
kubectl create -f nginx-deployment.yaml

# Get Pods
kubectl get pods
kubectl get pods -n mynamespace

# Delete Deployment
kubectl delete -f nginx-deployment.yaml

# Delete Namespace
kubectl delete namespace mynamespace