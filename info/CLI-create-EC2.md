# Create an EC2 instance with the AWS CLI

- Install the AWS CLI and configure it with your IAM user access keys.
- To launch an EC2 instance, you need the following parameters:

```bash
aws ec2 run-instances \
    --image-id "YOUR_AMI_ID" \
    --count 1 \
    --instance-type "YOUR_INSTANCE_TYPE" \
    --key-name "YOUR_KEY_NAME" \
    --security-group-ids "YOUR_SECURITY_GROUP_ID" \
    --subnet-id "YOUR_SUBNET_ID"
```

- Get your default VPC ID:

```bash
aws ec2 describe-vpcs
```

- Create a security group:

```bash
aws ec2 create-security-group \
    --group-name "YOUR_GROUP_NAME" \
    --description "ENTER_DESCRIPTION" \
    --vpc-id "YOUR_VPC_ID"
```

- Create an inbound rule, for example, to allow SSH:

```bash
aws ec2 authorize-security-group-ingress \
    --group-id "YOUR_GROUP_ID" \
    --protocol tcp \
    --port 22 \
    --cidr "YOUR_CIDR_BLOCK"
```

- Create a key pair to SSH into the instance after it is created. The `KeyMaterial` query returns the unencrypted PEM content for the private key. Run the command with `--help` to see more options:

```bash
aws ec2 create-key-pair \
    --key-name "YOUR_KEY_NAME" \
    --query 'KeyMaterial' \
    --output text > "YOUR_PEM_FILE"
```

- To get the subnet ID, run `aws ec2 describe-subnets` and choose the subnet ID in the availability zone where you want to deploy.
- Launch the instance and confirm creation with `aws ec2 describe-instances`; you can also see the public IP here.
- Restrict permissions on the new PEM file to read-only for you (the owner):

```bash
chmod 400 <YOUR_PEM_FILE>
```

- Connect to the instance via SSH:

```bash
ssh -i <YOUR_PEM_FILE> ec2-user@<INSTANCE_PUBLIC_IP>
```