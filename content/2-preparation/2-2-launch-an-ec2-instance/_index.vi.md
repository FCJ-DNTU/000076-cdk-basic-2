+++
title = "Chạy một máy chủ EC2"
date = 2024
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### Tạo một máy chủ EC2

1. Truy cập vào trong [AWS Management Console](https://aws.amazon.com/console/)

  - Tìm **EC2**
  - Chọn **EC2**

![find-ec2](/images/2-prepartion/2.7-find-ec2.png)

{{% notice note %}}
Bạn cần phải khởi động một máy chủ EC2 cho môi trường thực hành, sau đó là kết nối tới máy chủ này với SSH thông qua Visual Studio Code. Máy chủ EC2 sẽ được khởi động ở trong VPC mặc định.
{{% /notice %}}

2. Trong giao diện **EC2**

  - Chọn **EC2**
  - Chọn **Launch instance**

![lauch-instance](/images/2-prepartion/2.8-lauch-instance.png)

3. Ở trong phần **Application and OS Images**

  - **Name**: `cdk-workspace-ec2`
  - Chọn **Amazon Linux**
  - **AMI**: `Amazon Linux 2 AMI (HVM) Kernel 5.10, SSD Volume Type- Free tier eligble`
  - **Architecture**: `64-bit (x86)`

![ec2-settings-1](/images/2-prepartion/2.9-ec2-settings-1.png)

4. Chọn Instance type và tạo mới / chọn key-pair có sẵn

  - Chọn `t3.small`
  - Chọn một key-pair nếu như mình đã có các key-pair. Nếu không thì tạo như sau:
    - Chọn **Create new key pair**
    - **Name**: `my-key`
    - **Type**: `RSA`
    - **Private key file**: `.pem`
    - Chọn **Create key pair**

![ec2-settings-2](/images/2-prepartion/2.10-ec2-settings-2.png)

5. Trong phần **Network settings**

  - Để mọi thứ như mặc định mặc định, nhưng:
    - Mục Auto-assign public IP thì chọn **enable**
    - Mục Allow SSH traffic from thì chọn **Anywhere** (0.0.0.0/0)

![ec2-settings-3](/images/2-prepartion/2.11-ec2-settings-3.png)

1. Chọn **Launch** và chờ kết quả

![check-ec2-launch](/images/2-prepartion/2.12-check-ec2-launch.png)