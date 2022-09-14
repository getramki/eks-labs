Assume EKSAdmin Role
-------------------------------------------------
# 1. Create EKS Cluster IAM Role with AmazonEKSClusterPolicy
# 2. Create VPC and Cluster Security Group
Cloud Formation URL https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml
# 3. Create Cluster - Name: Demo-csl
# 4. Select VPC
# 5. Select Subnets
# 6. Select Security Groups
# 7. Select Cluster Endpoint Access - Public and Private
# 8. Default Networking add-ons
# 9. Create Cluster
-------------------------------------------
# 10. Create kubeconfig
aws eks --region us-west-2 update-kubeconfig --name Demo-csl

# 11. Try connecting kubectl get svc
# 12. If unauthorized - login with user / role who created the cluster
# 13. Add CLI User in ConfigMap of Cluster

eksctl create iamidentitymapping --region us-west-2 --cluster Demo-csl --arn arn:aws:iam::<Replace-With-AWS-Account-ID>:user/<Replace-with-AWS-IAM-Username> --group system:masters --username <Replace-with-AWS-IAM-Username>

eksctl create iamidentitymapping --region us-west-2 --cluster Demo-csl --arn arn:aws:iam::<Replace-With-AWS-Account-ID>:user/<Replace-with-AWS-IAM-Username> --group system:masters --username <Replace-with-AWS-IAM-Username>

eksctl create iamidentitymapping --region us-west-2 --cluster dev-cluster --arn <ARN> --group system:masters --username awsstudent

# OR

eksctl create iamidentitymapping --region us-west-2 --cluster Demo-csl --arn arn:aws:iam::<Replace-With-AWS-Account-ID>:user/<Replace-with-AWS-IAM-Console-Username> --group system:masters --username <Replace-with-AWS-IAM-Console-Username>