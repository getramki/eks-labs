# Describe the ebs-sc storage class.
kubectl describe storageclass ebs-sc

# Watch the pods in the default namespace
kubectl get pods --watch

# List the persistent volumes in the default namespace
kubectl get pv

# Verify that the pod is successfully writing data to the volume
kubectl exec -it ebs-demo-app -- cat /data/out.txt