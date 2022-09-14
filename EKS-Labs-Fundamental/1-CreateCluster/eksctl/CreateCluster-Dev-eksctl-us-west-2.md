# 1. Create EKS Cluster by eksctl with file (Will take 20 to 30 minutes to create)
cd E:\Workspace\EKS-Labs-Fundamental\1-CreateCluster\eksctl
eksctl create cluster -f devcluster-addons-us-west-2.yaml

# 2. Add Console User to EKS Cluster (Not Needed to do if console user/role and cli user/role is same)
eksctl create iamidentitymapping --region us-west-2 --cluster Dev --arn arn:aws:iam::<Replace-With-AWS-Account-ID>:user/<Replace-with-AWS-IAM-Console-Username> --group system:masters --username <Replace-with-AWS-IAM-Console-Username>

--------------------------------------------------
# 3. Delete Clusters - Only after completion of Labs
eksctl delete cluster -f devcluster-addons-us-west-2.yaml

--------------------------------------------------
# Optionals
eksctl utils describe-addon-versions --cluster Dev --region us-west-2