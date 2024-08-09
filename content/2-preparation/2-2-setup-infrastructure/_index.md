+++
title = "Setup infrastructure"
date = 2024
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### Setup network infrastructure

1. Access the [AWS Management Console](https://aws.amazon.com/console/)

   - Find **VPC**
   - Select **VPC**

IMAGE HERE

2. In the **VPC** interface

   - Select **VPC**
   - Select **Create VPC**

IMAGE HERE

3. In the **VPC settings** interface

   - At **Resource to create**, select **VPC and more** to create network infrastructure automatically
   - At **Name tag auto-generation**, check **Auto-generate** and enter the base name you want
   - And fill remaining information to form like below

IMAGE HERE

4. Select **Security Groups**, select **Create security group**

   - Select the **VPC** in previous step.
   - Inbound Rule is
     - Protocol: `All ICMP`; Source: `Internet Gateway`; IP: `0.0.0.0/0`
     - Protocol: `SSH`; Source: `My IP`. Temporarily, the SG only allows my device (IP) to access to EC2 via SSH.

IMAGE HERE