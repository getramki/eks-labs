# Create a repository for ecsdemo-crystal
aws ecr create-repository \
     --repository-name ecsdemo-crystal \
     --region us-west-2

# Create a repository for ecsdemo-frontend
aws ecr create-repository \
     --repository-name ecsdemo-frontend \
     --region us-west-2

# Create a repository for ecsdemo-nodejs
aws ecr create-repository \
     --repository-name ecsdemo-nodejs \
     --region us-west-2

---------------------------------------------------
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com

# Build Docker Image ecsdemo-crystal
cd ~/Code/EKS/MicroServices/ecsdemo-crystal
docker build -t ecsdemo-crystal .
docker tag ecsdemo-crystal:latest <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-crystal:latest
docker push <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-crystal:latest

# Build Docker Image ecsdemo-frontend
cd ~/Code/EKS/MicroServices/ecsdemo-frontend
docker build -t ecsdemo-frontend .
docker tag ecsdemo-frontend:latest <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-frontend:latest
docker push <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-frontend:latest

# Build Docker Image ecsdemo-nodejs
cd ~/Code/EKS/MicroServices/ecsdemo-nodejs
docker build -t ecsdemo-nodejs .
docker tag ecsdemo-nodejs:latest <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-nodejs:latest
docker push <Replace-With-AWS-Account-ID>.dkr.ecr.us-west-2.amazonaws.com/ecsdemo-nodejs:latest