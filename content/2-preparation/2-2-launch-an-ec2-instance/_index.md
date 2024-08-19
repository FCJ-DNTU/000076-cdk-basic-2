+++
title = "Launch an EC2 Instance"
date = 2024
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### Create an EC2 Instance

1. Access the [AWS Management Console](https://aws.amazon.com/console/)

  - Find **EC2**
  - Select **EC2**

![find-ec2](/images/2-prepartion/2.7-find-ec2.png)

{{% notice note %}}
You need to launch an EC2 instance for our environment, then connect to this EC2 via SSH with Visual Studio Code. The EC2 instance for workspace will be set up in default VPC.
{{% /notice %}}

2. In the **EC2** interface

  - Select **EC2**
  - Select **Launch instance**

![lauch-instance](/images/2-prepartion/2.8-lauch-instance.png)

3. In the **Application and OS Images**

  - **Name**: `cdk-workspace-ec2`
  - Select **Amazon Linux**
  - **AMI**: `Amazon Linux 2 AMI (HVM) Kernel 5.10, SSD Volume Type- Free tier eligble`
  - **Architecture**: `64-bit (x86)`

![ec2-settings-1](/images/2-prepartion/2.9-ec2-settings-1.png)

4. Select Instance type and create / select available key-pair

  - Select `t3.small`
  - Select a key-pair if you have a set of key-pair. If not:
    - Select **Create new key pair**
    - **Name**: `my-key`
    - **Type**: `RSA`
    - **Private key file**: `.pem`
    - Select **Create key pair**

![ec2-settings-2](/images/2-prepartion/2.10-ec2-settings-2.png)

5. In the **Network settings**

  - Leave everything else default, but:
    - Auto-assign public IP is **enable**
    - Allow SSH traffic from **Anywhere** (0.0.0.0/0)

![ec2-settings-3](/images/2-prepartion/2.11-ec2-settings-3.png)

1. Select **Launch** and wait for the result

![check-ec2-launch](/images/2-prepartion/2.12-check-ec2-launch.png)