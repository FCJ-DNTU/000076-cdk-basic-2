+++
title = "Create workspace"
date = 2024
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### Create workspace

1. Access the [AWS Management Console](https://aws.amazon.com/console/)

  - Find **EC2**
  - Select **EC2**

IMAGE HERE

{{% notice note %}}
You need to launch an EC2 instance for our environment, then connect to this EC2 via SSH with Visual Studio Code. The EC2 instance for workspace will be set up in default VPC
{{% /notice %}}

1. In the **EC2** interface

  - Select **EC2**
  - Select **Launch instance**

IMAGE HERE

3. Enter EC2 instance's name, OS and Application

  - **Name**: `workspace`
  - **Application and OS Images**: `Ubuntu`

IMAGE HERE

4. Configure **AMI** and **Instance type**

  - Select **Amazon Linux**
  - **AMI**: `Amazon Linux 2 AMI (HVM) Kernel 5.10, SSD Volume Type- Free tier eligble`
  - **Architecture**: `64-bit (x86)`
  - **Instance type**: `t3.small`

IMAGE HERE

5. Create key-pair

  - Select **Create new key pair**
  - **Name**: `my-key`
  - **Type**: `RSA`
  - **Private key file**: `.pem`
  - Select **Create key pair**

IMAGE HERE

6. In the **Network settings**

  - Select **Enable** in Auto-assign public IP
  - Leave everything else default

IMAGE HERE

7. Select **Launch** and wait for the result

IMAGE HERE