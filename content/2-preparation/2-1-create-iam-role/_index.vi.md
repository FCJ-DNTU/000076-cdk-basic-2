+++
title = "Tạo IAM Role"
date = 2024
weight = 1
chapter = false
pre = "<b>2.1 </b>"
+++

#### Tạo IAM Role

1. Truy cập vào [AWS Management Console](https://aws.amazon.com/console/)

  - Tìm **IAM**
  - Chọn **IAM**

![find-iam](/images/2-prepartion/2.1-find-iam.png)

2. Trong giao diện **IAM**

  - Chọn **Roles**
  - Chọn **Create role**

![choose-role](/images/2-prepartion/2.2-choose-role.png)

3. Trong giao diện **Select trusted entity**

  - Chọn **AWS Service**
  - **Use case**, Chọn EC2
  - Chọn **Next**

![choose-role-as-service](/images/2-prepartion/2.3-choose-role-as-service.png)

4. Trong giao diện **Create role**

  - Tìm chính sách (quyền) có tên là **AdministratorAccess**
  - Chọn **AdministratorAccess**
  - Chọn **Next**

![give-admin-access](/images/2-prepartion/2.4-give-admin-access.png)

5. Trong giao diện **Role details**

  - Ở **Role name**, nhập `CDK-Role`

![role-settings](/images/2-prepartion/2.5-role-settings.png)

6. Chọn **Create role**

![complete-role](/images/2-prepartion/2.6-complete-role.png)

7. Hoàn thành bước tạo role
