# jenkins_backup

🚀 Launch Two EC2 Instances with S3 Access
1. Create an IAM Role with S3 Full Access
Navigate to the AWS IAM console.

Create a new role with the following settings:

Trusted entity: AWS service

Use case: EC2

Permissions: Attach the AmazonS3FullAccess policy

Name the role (e.g., JenkinsS3AccessRole) and create it.

Configure your AWS Credential

2. Launch Two EC2 Instances
Go to the EC2 dashboard and launch two instances:

AMI: Choose an appropriate Amazon Machine Image (e.g., Ubuntu 20.04)

Instance Type: Select based on your requirements (e.g., t2.micro)

IAM Role: Attach the previously created JenkinsS3AccessRole

Complete the remaining steps and launch the instances.

Configure your AWS Credential


# Update and install Java
```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```

# Add Jenkins repo and install
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
```

# (Plugin required): Nexus Artifact Uploader Plugin
# Search manually via: https://plugins.jenkins.io/nexus-artifact-uploader/

# Install dependencies for AWS CLI
```bash
sudo apt update
sudo apt install unzip curl -y
sudo apt install python3-pip -y
```
# Download and install AWS CLI v2
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```
# Backup Jenkins Home
```bash
sudo tar -czpf jenkins_backup.tar.gz /var/lib/jenkins/
```
# Upload backup to S3
```bash
aws s3 cp jenkins_backup.tar.gz s3://jenkinsbackup085/
```
# Verify backup in S3
```bash
aws s3 ls s3://jenkinsbackup085/
```

# Install Jenkins same as above
```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
```

# Stop Jenkins before restore
```bash
sudo systemctl stop jenkins
```

# Download backup from S3
```bash
aws s3 cp s3://jenkinsbackup085/jenkins_backup.tar.gz jenkins_backup.tar.gz
```
# Extract backup to root (will restore to /var/lib/jenkins)
```bash
sudo tar -zxvf jenkins_backup.tar.gz -C /
```
# Final update and start Jenkins
```bash
sudo apt update
sudo systemctl start jenkins
```
