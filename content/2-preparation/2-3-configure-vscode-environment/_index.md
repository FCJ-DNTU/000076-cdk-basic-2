+++
title = "Configure VSCode environment"
date = 2024
weight = 3
chapter = false
pre = "<b>2.3 </b>"
+++

#### Configure VSCode environment

1. Open the Visual Studio Code in your PC

    - Select the **Extension** icon or use **Ctrl + Shift + X**
    - Find `Remote SSH`, select **Remote - SSH** and **Remote - SSH: Editing Configuration Files**
    - Install these extensions.

![install-ssh-extensions-1](/images/2-prepartion/2.13-install-ssh-extensions-1.png)
![install-ssh-extensions-2](/images/2-prepartion/2.13-install-ssh-extensions-2.png)

2. After the installation is complete, open Command Palette with `Ctrl + Shift + P`

    - Find `Remote-SSH: Add New SSH Host`.
    - Enter `cdk-workspace-ec2`
    - Then select **C:\\...\\.ssh\config**, then the configuration file will be opened

![add-new-ssh-host](/images/2-prepartion/2.14-add-new-ssh-host.png)
![enter-ssh-host](/images/2-prepartion/2.15-enter-ssh-host.png)
![open-config](/images/2-prepartion/2.16-open-config.png)

The configuration of SSH Host will have the format like below
```
Host cdk-workspace
  HostName x.x.x.x
  User ec2-user
  IdentityFile D:\path\to\private-key.pem
```

![update-file-config](/images/2-prepartion/2.18-update-file-config.png)

3. We'll connect to the host which we've just added

    - Find `Remote-SSH: Connect Current Windows to Host`
    - Select the host `cdk-workspace`
    - Select `Linux`
    - Select `Continue`

![connect-to-host](/images/2-prepartion/2.19-connect-to-host.png)
![select-host](/images/2-prepartion/2.20-select-host.png)
![select-os](/images/2-prepartion/2.21-select-os.png)
![select-continue](/images/2-prepartion/2.22-select-continue.png)

After a few seconds, the connection is established successfully. Now you can open EC2's directories in your VSCode

4. Now, we'll open the root directory

![open-directory](/images/2-prepartion/2.23-open-directory.png)
![main-directory](/images/2-prepartion/2.24-main-directory.png)

5. We'll attach IAM Role to EC2 Instance, back to **EC2** console, in **Actions**

    - Select **Security**
    - Select **Modify IAM Role**
    - Select the IAM Role named `CDK-Role` which you created before

![add-iam-role-to-ec2](/images/2-prepartion/2.25-add-iam-role-to-ec2.png)
![update-iam-role](/images/2-prepartion/2.26-update-iam-role.png)

6. Next, copy and Paste the command below into the Terminal of VSCode Workspace to install tools to support text processing on the command line.

```
sudo yum -y install jq gettext bash-completion moreutils
```

![add-some-utils](/images/2-prepartion/2.27-add-some-utils.png)

7. Install `python 3.9` and `pip`

Firstly, we need to install these binaries to support python3.9

```
sudo yum install gcc openssl-devel bzip2-devel libffi-devel
```

![install-support-bin](/images/2-prepartion/2.28-install-support-bin.png)

Enter `/opt` directory and get `python3.9` compress file

```
cd /opt
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
sudo tar xzf Python-3.9.16.tgz
```

Enter `Python-3.9.16 ` directory and extract the downloaded file, and run configure

```
cd Python-3.9.16
sudo ./configure --enable-optimizations
sudo make altinstall
```

![install-pyton-1](/images/2-prepartion/2.29-install-pyton-1.png)

{{% notice note %}}
In Amazon Linux 2, python2.7 is availabel, but AWS CDK needs versions of python >= 3.8. 
{{% /notice %}}

![install-pyton-2](/images/2-prepartion/2.30-install-python-2.png)
![2.31-python-pip-are-installed](/images/2-prepartion/2.31-python-pip-are-installed.png)

8. Similar to CloudFormation, you can install the cfn-lint tool to help you check CDK templates and other information, including auditing. Check if the resource properties are correct or not configured according to best practices or not.

```
pip install cfn-lint
```
And check the successful installation of cfn-lint using the following command:
```
cfn-lint --version
```

![install-cfn-lint](/images/2-prepartion/2.32-install-cfn-lint.png)
![check-cfn-lint-install](/images/2-prepartion/2.33-check-cfn-lint-install.png)

9. Setup environment variables to let aws cli use the current region

Before we can get metadata of EC2, we need to modify the instance metadata options

![modify-instance-metadata-1](/images/2-prepartion/2.33-modify-instance-metadata-1.png)
![modify-instance-metadata-2](/images/2-prepartion/2.34-modify-instance-metadata-2.png)

Make sure we can get the EC2 instance's metadata

![check-instance-metadata](/images/2-prepartion/check-instance-metadata.png)

Set up environment variables

```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

Save the configuration information to bash_profile

```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile

echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile

echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile

aws configure set default.region ${AWS_REGION}
```

![setup-env-add-to-bash-profile](/images/2-prepartion/2.35-setup-env-add-to-bash-profile.png)

10.  The CDK isn't installed, we have to install it with NPM. Install NodeJS with NVM

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

![install-nvm](/images/2-prepartion/2.36-install-nvm.png)

11. Install NodeJS and AWS CDK and check if **node** and **npm** are available.
```
nvm install 20
node -v
npm -v
npm install -g aws-cdk
```

![install-nodejs](/images/2-prepartion/2.37-install-nodejs.png)
![install-cdk](/images/2-prepartion/2.38-install-cdk.png)

12. We will use the command to check if the EC2 instance is using the IAM Role correctly.
```
aws sts get-caller-identity --query Arn | grep CDK-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

![check-if-role-is-valid](/images/2-prepartion/2.39-check-if-role-is-valid.png)