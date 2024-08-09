+++
title = "Configure VSCode environment"
date = 2024
weight = 3
chapter = false
pre = "<b>2.3 </b>"
+++

#### Configure VSCode environment

1. Open the Visual Studio Code

  - Select the **Extension** icon or `Ctrl + Shift + X`
  - Find `Remote- SSH`, select **Remote- SSH** and **Remote- SSH: Editing Configuration Files**
  - Install these extension.

IMAGE HERE

2. After the installation is complete, open Command Palette with `Ctrl + Shift + P`

  - Find `Remote-SSH: Open SSH Configuration File`, select **C:\\...\\.ssh\config**, then the configuration file will be opened

Configuration will have the format like below
```
Host cdk-workspace
  HostName x.x.x.x
  User ec2-user
  IdentityFile D:\path\to\private-key.pem
```

  - **x.x.x.x** is your EC2's public IPv4 Address
  - Save file and close

IMAGE HERE

3. Open the Visual Studio Code

  - Find `Remote-SSH: Connect Current Windows to Host`
  - Select the host `cdk-workspace`
  - Select `Linux`
  - Select `Continue`

After a few seconds, the connection is established successfully. Now you can open EC2's directories in your VSCode

IMAGE HERE

4. We'll attach IAM Role to EC2 Instance, back to **EC2** console, in **Actions**

  - Select **Security**
  - Select **Modify IAM Role**
  - Select the IAM Role named `CDK-Role` which you created before

IMAGE HERE

5. Next, copy and Paste the command below into the Terminal of Cloud9 Workspace to install tools to support text processing on the command line.

```
sudo yum -y install jq gettext bash-completion moreutils
```

IMAGE HERE

7. Similar to CloudFormation, you can install the cfn-lint tool to help you check CDK templates and other information, including auditing. Check if the resource properties are correct or not configured according to best practices or not.

```
pip install cfn-lint
```
And check the successful installation of cfn-lint using the following command:
```
cfn-lint --version
```


IMAGE HERE

8. Setup environment variables to let aws cli use the current region

```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

IMAGE HERE

9. We will save the configuration information to bash_profile

```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile

echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile

echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile

aws configure set default.region ${AWS_REGION}
```

IMAGE HERE

10. The CDK isn't installed, we have to install it with NPM. Install NodeJS with NVM

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

After NVM is installed, we have to set the environment variables like:
IMAGE HERE

11. Install NodeJS and AWS CDK
```
nvm install 20
node -v
npm -v
npm install -g aws-cdk
```

IMAGE HERE

12. Check the installation of AWS CDK using the following command:
```
cdk --version
```

IMAGE HERE

13. We will use the command to check if the Cloud9 IDE is using the IAM Role correctly.
```
aws sts get-caller-identity --query Arn | grep CDK-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```