# Create Managed Node Group in Dev Cluster
eksctl create nodegroup \
  --cluster Dev \
  --region us-west-2 \
  --name mng2 \
  --node-type t3.medium \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 4
------------------------------------------

# Delete Node Group
eksctl delete nodegroup \
  --cluster Dev \
  --region us-west-2 \
  --name mng2

eksctl delete nodegroup \
  --cluster Dev \
  --region us-west-2 \
  --name ng-b9dafa1c