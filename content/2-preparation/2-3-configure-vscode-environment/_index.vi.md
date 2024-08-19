+++
title = "Cấu hình môi trường VSCode"
date = 2024
weight = 3
chapter = false
pre = "<b>2.3 </b>"
+++

#### Cấu hình môi trường VSCode

1. Mở Visual Studio Code trong máy vi tính cá nhân của bạn

    - Chọn biểu tượng **Extension** hoặc dùng tổ hợp phím **Ctrl + Shift + X** để mở giao diện **Extension**.
    - Tìm `Remote SSH`, chọn **Remote - SSH** và **Remote - SSH: Editing Configuration Files**
    - Cài các gói mở rộng này vào trong máy.

![install-ssh-extensions-1](/images/2-prepartion/2.13-install-ssh-extensions-1.png)
![install-ssh-extensions-2](/images/2-prepartion/2.13-install-ssh-extensions-2.png)

2. Sau khi cài đặt xong, thì mình mở ô nhập lệnh với tổ hợp phím `Ctrl + Shift + P`

    - Tìm `Remote-SSH: Add New SSH Host`.
    - Nhập `cdk-workspace-ec2`.
    - Sau đó là chọn **C:\\...\\.ssh\config**, và một tệp tin cấu hình được mở lên.

![add-new-ssh-host](/images/2-prepartion/2.14-add-new-ssh-host.png)
![enter-ssh-host](/images/2-prepartion/2.15-enter-ssh-host.png)
![open-config](/images/2-prepartion/2.16-open-config.png)

Phần cấu hình của SSH Host có dạng như bên dưới:
```
Host cdk-workspace
  HostName x.x.x.x
  User ec2-user
  IdentityFile D:\path\to\private-key.pem
```

![update-file-config](/images/2-prepartion/2.18-update-file-config.png)

3. Mình sẽ kết nối tới máy chủ EC2 mà mình vừa tạo hổi này với các cấu hình mới thiết lập

    - Tìm `Remote-SSH: Connect Current Windows to Host`
    - Chọn máy chủ `cdk-workspace`
    - Chọn `Linux`
    - Chọn `Continue`

![connect-to-host](/images/2-prepartion/2.19-connect-to-host.png)
![select-host](/images/2-prepartion/2.20-select-host.png)
![select-os](/images/2-prepartion/2.21-select-os.png)
![select-continue](/images/2-prepartion/2.22-select-continue.png)

Sau một vài giây, thì kết nối đã được thiết lập thành công. 

4. Giờ thì chúng ta có thể mở các thư mục ở trong máy chủ EC2 ở trong VSCode.

![open-directory](/images/2-prepartion/2.23-open-directory.png)
![main-directory](/images/2-prepartion/2.24-main-directory.png)

5. Chúng ta sẽ gắn IAM Role đã tạo ở bước trước vào cho máy chủ EC2, trở lại với giao diện **EC2**, trong phần **Actions**

    - Chọn **Security**
    - Chọn **Modify IAM Role**
    - Chọn IAM Role tên là `CDK-Role` mà mình đã tạo hồi nãy

![add-iam-role-to-ec2](/images/2-prepartion/2.25-add-iam-role-to-ec2.png)
![update-iam-role](/images/2-prepartion/2.26-update-iam-role.png)

6. Tiếp theo, sao chép và dán lệnh dưới đây vào trong phần Terminall của VSCode để cài đặt các công cụ cần thiết cho việc xử lý từ trong command line.
```
sudo yum -y install jq gettext bash-completion moreutils
```

![add-some-utils](/images/2-prepartion/2.27-add-some-utils.png)

7. Cài đặt `python 3.9` và `pip`

Đầu tiên, mình sẽ cần phải cài các tệp nhị phân để hỗ trợ python3.9

```
sudo yum install gcc openssl-devel bzip2-devel libffi-devel
```

![install-support-bin](/images/2-prepartion/2.28-install-support-bin.png)

Vào trong thư mục `/opt` và tải tệp `python3.9` đã được nén

```
cd /opt
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
sudo tar xzf Python-3.9.16.tgz
```

Vào trong thư mục và giải nén tệp `Python-3.9.16` vừa tải về, sau đó tiến hành chạy các cấu hình

```
cd Python-3.9.16
sudo ./configure --enable-optimizations
sudo make altinstall
```

![install-pyton-1](/images/2-prepartion/2.29-install-pyton-1.png)

{{% notice note %}}
AWS CDK cần phiên bản của python lớn hơn hoặc bằng 3.8
{{% /notice %}}

![install-pyton-2](/images/2-prepartion/2.30-install-python-2.png)
![2.31-python-pip-are-installed](/images/2-prepartion/2.31-python-pip-are-installed.png)

8. Giống với CloudFormation, mình có thể cài đặt cfn-lint để giúp mình có thể kiểm tra các mẫu CDK và các thông tin khác, bao gồm việc kiểm trả. Kiểm tra xem nếu như các tài nguyên đã được cài hay chưa và có được cài đúng với quy tắc tốt nhất hay không.

```
pip install cfn-lint
```
Mình có thể kiểm tra xem cfn-lint đã được cài thành công chưa với lệnh bên dưới:
```
cfn-lint --version
```

![install-cfn-lint](/images/2-prepartion/2.32-install-cfn-lint.png)
![check-cfn-lint-install](/images/2-prepartion/2.33-check-cfn-lint-install.png)

9. Thiết lập môi biến môi trường và để cho aws cli dùng mã của vùng hiện tại

Trước khi mình có thể dùng thông tin của máy chủ EC2, mình cần phải điều chỉnh lại lựa chọn thông tin của máy chủ

![modify-instance-metadata-1](/images/2-prepartion/2.33-modify-instance-metadata-1.png)
![modify-instance-metadata-2](/images/2-prepartion/2.34-modify-instance-metadata-2.png)

Để chắc chắn rằng mình có thể lấy thông tin của máy chủ mình đã tạo hồi nãy

![check-instance-metadata](/images/2-prepartion/check-instance-metadata.png)

Thiết lập các biến môi trường

```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

Lưu các thông tin đó vào trong bash_profile

```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile

echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile

echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile

aws configure set default.region ${AWS_REGION}
```

![setup-env-add-to-bash-profile](/images/2-prepartion/2.35-setup-env-add-to-bash-profile.png)

10. CDK chưa được cài đặt, giờ mình sẽ cài nó với NPM từ NodeJS, trước tiên là cài NVM trước

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

![install-nvm](/images/2-prepartion/2.36-install-nvm.png)

11. Sau đó là cài NodeJS và cài AWS CDK (thư viện toàn cục) và kiểm tra xem là **node** và **npm** đã có hay chưa
```
nvm install 20
node -v
npm -v
npm install -g aws-cdk
```

![install-nodejs](/images/2-prepartion/2.37-install-nodejs.png)
![install-cdk](/images/2-prepartion/2.38-install-cdk.png)

12. Mình sẽ dùng lệnh dưới đây để kiểm tra xem là máy của EC2 của mình có thể dùng IAM Role mà mình mới gắn từ nãy hay chưa
```
aws sts get-caller-identity --query Arn | grep CDK-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

![check-if-role-is-valid](/images/2-prepartion/2.39-check-if-role-is-valid.png)