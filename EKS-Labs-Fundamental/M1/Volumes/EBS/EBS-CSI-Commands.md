# To deploy the Amazon EBS CSI driver to an Amazon EKS cluster
# Create an IAM policy that allows the CSI driver's service account to make calls to AWS APIs on your behalf
# --Done -- Download IAM Policy
curl -o example-iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-ebs-csi-driver/release-1.3/docs/example-iam-policy.json

# Create Policy
aws iam create-policy \
    --policy-name AmazonEKS_EBS_CSI_Driver_Policy \
    --policy-document file://example-iam-policy.json

# Create an IAM role and attach the IAM policy to it
eksctl create iamserviceaccount \
    --name ebs-csi-controller-sa \
    --namespace kube-system \
    --cluster Dev \
    --attach-policy-arn arn:aws:iam::<Replace-With-AWS-Account-ID>:policy/AmazonEKS_EBS_CSI_Driver_Policy \
    --approve \
    --override-existing-serviceaccounts

# Retrieve the ARN of the created role and note the returned value
aws cloudformation describe-stacks \
    --stack-name eksctl-Dev-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa \
    --query='Stacks[].Outputs[?OutputKey==`Role1`].OutputValue' \
    --output text

# Role ARN Value
arn:aws:iam::<Replace-With-AWS-Account-ID>:role/eksctl-Dev-addon-iamserviceaccount-kube-syst-Role1-AJN0V3I853AR

# Deploy the driver using Helm
helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver
helm repo update

# Install a release of the driver using the Helm chart
helm upgrade -install aws-ebs-csi-driver aws-ebs-csi-driver/aws-ebs-csi-driver \
    --namespace kube-system \
    --set image.repository=602401143452.dkr.ecr.us-west-2.amazonaws.com/eks/aws-ebs-csi-driver \
    --set controller.serviceAccount.create=false \
    --set controller.serviceAccount.name=ebs-csi-controller-sa

# To deploy a sample application and verify that the CSI driver is working
# Clone the Amazon EBS Container Storage Interface (CSI) driver
git clone https://github.com/kubernetes-sigs/aws-ebs-csi-driver.git

# Navigate to the dynamic-provisioning example directory
cd aws-ebs-csi-driver/examples/kubernetes/dynamic-provisioning/

# Deploy the ebs-sc storage class, ebs-claim persistent volume claim, and app sample application from the specs directory
kubectl apply -f specs/

# Describe the ebs-sc storage class.
kubectl describe storageclass ebs-sc

# Watch the pods in the default namespace
kubectl get pods --watch

# List the persistent volumes in the default namespace
kubectl get pv

# Verify that the pod is successfully writing data to the volume
kubectl exec -it app -- cat /data/out.txt

# delete the resources for this sample application to clean up
kubectl delete -f specs/