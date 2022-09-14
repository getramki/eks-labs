# Install Latest version of Kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Install version 1.21 of Kubectl
curl -LO https://dl.k8s.io/release/v1.21.0/bin/linux/amd64/kubectl

# Download the kubectl checksum file for latest version
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

# Download the kubectl checksum file for version 1.21
curl -LO "https://dl.k8s.io/v1.21.0/bin/linux/amd64/kubectl.sha256"

# Validate the kubectl binary against the checksum file:
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

# If valid, the output is:
kubectl: OK

# Install kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Test to ensure the version you installed is up-to-date:
kubectl version --client

