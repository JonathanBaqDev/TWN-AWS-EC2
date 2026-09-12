# Deploy React app to EC2

Example of deploying an application with Docker to an Amazon Elastic Compute Cloud (EC2) instance.

## Create EC2 instance
- In the AWS console, go to EC2 > Instances > Launch instance.
- A `t2.micro` instance is sufficient and eligible for the Free Tier.
- Create a key pair so you can SSH to the instance later.
- Leave the network settings at their defaults. The instance will launch in the default VPC with internet access and a public IP address.
- Create a security group with SSH access. Set the source to My IP to limit access.
- Leave the storage at 8 GB and launch the instance.

## Connect to the instance
- Move the SSH private key you downloaded when creating the key pair to a secure location, such as your local `.ssh` folder.
- Limit the key's permissions to read-only for your user account: `chmod 400 ~/.ssh/<private-key>`
- Copy the EC2 instance's public IP address.
- SSH to the instance as `ec2-user`, which is available by default: `ssh -i ~/.ssh/<private-key> ec2-user@<instance-public-IP>`

## Install Docker on the EC2 instance
- Update the package manager and install Docker:
```bash
sudo yum update -y
sudo yum install -y docker
```
- Verify the installation, start the Docker daemon, and confirm that it is running:
```bash
docker --version
sudo service docker start
ps aux | grep docker
```
- Add `ec2-user` to the `docker` group so the user can run Docker commands without `sudo`:
```bash
sudo usermod -aG docker ec2-user
exit
ssh -i ~/.ssh/<private-key> ec2-user@<instance-public-IP>
groups
docker ps
```

## Run the Docker container from a private Docker Hub repository
- Build the application image and push it to Docker Hub. Create an access token in your Docker Hub account for CLI logins:
```bash
docker build -t <dockerhub_username>/<repo_name>:react-node-1.0.0 .

# Log in to Docker Hub without exposing the token in the command history.
read -rsp "Docker Hub token: " DOCKERHUB_TOKEN
echo
printf '%s' "$DOCKERHUB_TOKEN" | docker login \
  --username <dockerhub_username> \
  --password-stdin
unset DOCKERHUB_TOKEN

docker push <dockerhub_username>/<repo_name>:react-node-1.0.0
```
- On the EC2 instance, log in to Docker Hub using the same method and fetch the application image:
```bash
docker pull <dockerhub_username>/<repo_name>:react-node-1.0.0
docker images
docker run -d -p 3080:3080 <dockerhub_username>/<repo_name>:react-node-1.0.0
docker ps
```

## Configure the EC2 security group to allow access to the app
- In the AWS console, go to EC2 > Instances.
- Select your instance, then choose Security > Security groups.
- SSH access via TCP/22 was configured when the instance was created.
- In the security group, choose Edit inbound rules > Add rule > Custom TCP, and allow port `3080` from an appropriate source.
- Open `http://<your-ec2-public-IP>:3080` in a browser.