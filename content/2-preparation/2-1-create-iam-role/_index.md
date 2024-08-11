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

![find-iam](/images/2-prepartion/2.1-find-iam.png)

2. In the **IAM** interface

  - Select **Roles**
  - Select **Create role**

![choose-role](/images/2-prepartion/2.2-choose-role.png)

3. In the **Select trusted entity** interface

  - Select **AWS Service**
  - **Use case**, select EC2
  - Select **Next**

![choose-role-as-service](/images/2-prepartion/2.3-choose-role-as-service.png)

4. In the **Create role** interface

  - Find the policy **AdministratorAccess**
  - Select the policy **AdministratorAccess**
  - Select **Next**

![give-admin-access](/images/2-prepartion/2.4-give-admin-access.png)

5. In the **Role details** interface

  - **Role name**, enter `CDK-Role`

![role-settings](/images/2-prepartion/2.5-role-settings.png)

6. Select **Create role**

![complete-role](/images/2-prepartion/2.6-complete-role.png)

7. Complete role creation
