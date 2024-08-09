+++
title = "Create IAM Role"
date = 2024
weight = 1
chapter = false
pre = "<b>2.1 </b>"
+++

#### Create IAM Role

1. Access the [AWS Management Console](https://aws.amazon.com/console/)

  - Find **IAM**
  - Select **IAM**

IMAGE HERE

2. In the **IAM** interface

  - Select **Roles**
  - Select **Create role**

IMAGE HERE

3. In the **Select trusted entity** interface

  - Select **AWS Service**
  - **Use case**, select EC2
  - Select **Next**

IMAGE HERE

4. In the **Create role** interface

  - Find the policy **AdministratorAccess**
  - Select the policy **AdministratorAccess**
  - Select **Next**

IMAGE HERE

5. In the **Role details** interface

  - **Role name**, enter `CDK-Role`

IMAGE HERE

6. Select **Create role**

IMAGE HERE

7. Complete role creation
