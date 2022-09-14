# 1. Create IAM Open ID Connect provider
eksctl utils associate-iam-oidc-provider --region=us-west-2 --cluster=Dev --approve

# 2. Done ----- Create an IAM policy
aws iam create-policy \
    --policy-name EKSClusterAutoscalerPolicy \
    --policy-document file://cluster-autoscaler-policy.json

# 3. Create IAM Role and Attach IAM Policy for AutoScaling with eksctl
eksctl create iamserviceaccount --region us-west-2 \
  --cluster=Dev \
  --namespace=kube-system \
  --name=cluster-autoscaler \
  --attach-policy-arn=arn:aws:iam::<Replace-With-AWS-Account-ID>:policy/EKSClusterAutoscalerPolicy \
  --override-existing-serviceaccounts \
  --approve

# 4. Get IAM Role ARN created
eksctl get iamserviceaccount --region us-west-2 \
  --cluster=Dev \
  --namespace=kube-system \
  --name=cluster-autoscaler

# 5. Done ----- Download the Cluster Autoscaler YAML file -----
curl -o cluster-autoscaler-autodiscover.yaml https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml

# 6. Done ----- Modify the YAML file and replace <YOUR CLUSTER NAME> with your cluster name. 
Dev

# 7. Apply the YAML file to your cluster
kubectl apply -f dev-cluster-autoscaler-autodiscover.yaml

# 8. Annotate the cluster-autoscaler service account with the ARN of the IAM role
kubectl annotate serviceaccount cluster-autoscaler \
  -n kube-system \
  eks.amazonaws.com/role-arn=<arn:aws:iam::<Replace-With-AWS-Account-ID>:role/EKSClusterAutoscalerRole>

kubectl annotate serviceaccount cluster-autoscaler \
  -n kube-system \
  eks.amazonaws.com/role-arn=arn:aws:iam::<Replace-With-AWS-Account-ID>:role/eksctl-Dev-addon-iamserviceaccount-kube-syst-Role1-1Q2X0CN4GOGOY

-----------------------------------------------------------------------------
eksctl delete iamserviceaccount --region us-west-2 \
  --cluster=Dev \
  --namespace=kube-system \
  --name=cluster-autoscaler