# 1. Deploy Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.5/aio/deploy/recommended.yaml

# 2. Create an eks-admin service account and cluster role binding
# Done --- Create eks-admin-service-account.yaml
kubectl apply -f eks-admin-service-account.yaml

# 3. Connect to the Dashboard
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
# Retrieve an authentication token for the eks-admin service account. Copy the <authentication_token> value from the output

# 4. Start the kubectl proxy
kubectl proxy

# 5. Access the dashboard endpoint
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#!/login

# 6. Choose Token Auth and sign in
