# Create a Fargate profile
eksctl create fargateprofile \
  --cluster Dev \
  --name game-2048 \
  --namespace game-2048

# Get Fargate profile
eksctl get fargateprofile \
  --cluster Dev \
  -o yaml

# Check Helm
helm version

# Create IAM OIDC provider
eksctl utils associate-iam-oidc-provider \
    --region us-west-2 \
    --cluster Dev \
    --approve

# --Done-- Create an IAM policy
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.2.0/docs/install/iam_policy.json
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
rm iam_policy.json

# Create a IAM role and ServiceAccount for the Load Balancer controller
eksctl create iamserviceaccount \
    --cluster Dev \
    --namespace kube-system \
    --name aws-load-balancer-controller \
    --attach-policy-arn arn:aws:iam::<Replace-With-AWS-Account-ID>:policy/AWSLoadBalancerControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --approve

# See details of the service account created
kubectl get sa aws-load-balancer-controller -n kube-system -o yaml

# Install the TargetGroupBinding CRDs
kubectl apply -k github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master

# verify if the AWS Load Balancer Controller version has beed set
if [ ! -x ${LBC_VERSION} ]
  then
    tput setaf 2; echo '${LBC_VERSION} has been set.'
  else
    tput setaf 1;echo '${LBC_VERSION} has NOT been set.'
fi

# Add Helm repo eks charts
helm repo add eks https://aws.github.io/eks-charts

# Get VPC ID
export VPC_ID=$(aws eks describe-cluster \
                --name Dev \
                --query "cluster.resourcesVpcConfig.vpcId" \
                --output text)

# Helm deploy load Balancer
helm upgrade -i aws-load-balancer-controller \
    eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=Dev \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set image.tag="${LBC_VERSION}" \
    --set region=us-west-2 \
    --set vpcId=${VPC_ID}

# Check if the deployment has completed
kubectl -n kube-system rollout status deployment aws-load-balancer-controller

# Deploy the game 2048 as a sample application to verify that the AWS Load Balancer Controller creates an Application Load Balancer
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/examples/2048/2048_full.yaml

# check if the deployment has completed
kubectl -n game-2048 rollout status deployment deployment-2048

# list all the nodes in the EKS cluster
kubectl get nodes

# verify that the Ingress resource is enabled
kubectl get ingress/ingress-2048 -n game-2048
---------------------------------------------------------

# Cleaning up
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/examples/2048/2048_full.yaml

helm uninstall aws-load-balancer-controller \
    -n kube-system

eksctl delete iamserviceaccount \
    --cluster Dev \
    --name aws-load-balancer-controller \
    --namespace kube-system \
    --wait

aws iam delete-policy \
    --policy-arn arn:aws:iam::<Replace-With-AWS-Account-ID>:policy/AWSLoadBalancerControllerIAMPolicy

kubectl delete -k github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master

eksctl delete fargateprofile \
  --name game-2048 \
  --cluster Dev

kubectl -n game-2048 delete deployment deployment-2048