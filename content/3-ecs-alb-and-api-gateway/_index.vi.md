+++
title = "ECS, ALB và API Gateway"
date = 2024
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

#### Tạo cụm ECS và Appilcation Load Balancer

1. Truy cập vào trong VSCode
    - Tạo một thư mục mới tên là `cdk-workshop-02`
    - Vào trong thư mục mới `cdk-workshop-02`
    - Tạo một dự án CDK mới với ngôn ngữ là `python`

![create-work-folder](/images/3-ecs-alb-and-api-gateway/3.1-create-work-folder.png)

2. Vào trong tệp tin vừa được tạo với CDK là `cdk-workshop-02/cdk_workshop_02_stack.py`, thêm các thư viện này vào trong tệp tin

```
from aws_cdk import (
  Stack,
  aws_ecs as ecs,
  aws_ecs_patterns as ecsp,
  aws_apigateway as apigateway
)
from constructs import Construct
```

3. Trong `cdk-workshop-02/cdk_workshop_02_stack.py`, khai báo một cụm ECS với Cân bằng tải ứng dụng (ALB) trong hàm `__init__()`

```py
      # Declare a Load Balancer Fargate 
      lb_fargate_service = ecsp.ApplicationLoadBalancedFargateService(
				self, 
				"Workshop02-MyWebServer",
        task_image_options=ecsp.ApplicationLoadBalancedTaskImageOptions(
            image=ecs.ContainerImage.from_registry("nginxdemos/hello")
        ),
        public_load_balancer=True,
        desired_count=3
      )
```
`ecsp.ApplicationLoadBalancedFargateService` sẽ tạo ra các tài nguyên như sau:

  - Một VPC mới được tạo ra với cấu hình mặc định (subnet, Internet Gateway, RouteTable, EIP, Nat Gateway, …)
  - Một cụm ECS được tạo mới trong VPC đó, và chạy trong Fargate
  - Một ECS Task được định nghĩa với bản sao `nginxdemos/hello` từ Dockerhub
  - Một nhóm ECS Container được khởi tạo từ ECS Task, với số lượng là 3
  - Một Application Load Balancer được đặt ở trước cụm ECS đó
  - Các roles và security groups cần thiết

#### Configure API Gateway

4. Khai báo một **API Gateway**

```py
# Define API Gateway
api = apigateway.RestApi(self, "ProxyToLBECS")
```

5. Cầu hình tài nguyên **API Gateway** để trỏ về DNS của **Application Load Balancer**

```py
# Get the DNS value of the Application Load Balancer 
lb = lb_fargate_service.load_balancer
lb_dns = lb_fargate_service.load_balancer.load_balancer_dns_name

# Add resource and method for proxy request
proxy = api.root.add_resource("ecs")
proxy.add_method("GET", apigateway.HttpIntegration(f"http://{lb_dns}"))
```

Bạn có thể đọc thêm về các phương thức và thuộc tính của ApplicationLoadBalancedFargateService tại [the official CDK documentation of AWS (Python)](https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_ecs_patterns/ApplicationLoadBalancedFargateService.html)

6. Kiểm tra lại mã ở trong `cdk-workshop-02/cdk_workshop_02_stack.py`

```py
from aws_cdk import (
    Stack,
    aws_ecs as ecs,
    aws_ecs_patterns as ecsp,
    aws_apigateway as apigateway
)
from constructs import Construct

class CdkWorkshop02Stack(Stack):

    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)
        
        # Declare a Load Balancer Fargate 
        lb_fargate_service = ecsp.ApplicationLoadBalancedFargateService(
          self, 
          "Workshop02-MyWebServer",
          task_image_options=ecsp.ApplicationLoadBalancedTaskImageOptions(
              image=ecs.ContainerImage.from_registry("nginxdemos/hello")),
          public_load_balancer=True,
          desired_count=3
        )
        
        # Define API Gateway
        api = apigateway.RestApi(self, "ProxyToLBECS")
        
        # Get the DNS value of the Application Load Balancer 
        lb = lb_fargate_service.load_balancer
        lb_dns = lb_fargate_service.load_balancer.load_balancer_dns_name
        
        # Add resource and method for proxy request
        proxy = api.root.add_resource("ecs")
        proxy.add_method("GET", apigateway.HttpIntegration(f"http://{lb_dns}"))
```

#### Khởi tạo một môi trường ảo cho Python
Trước khi mà chúng ta có thể triển khai lên Cloud, thì
  - Khởi tạo một môi trường ảo trong thư mục `cdk-workshop-02`, vì máy chủ của chúng ta đang có 2 phiên bản python, nên chúng ta cần phải cô lập các thư viện python ở mỗi phiên bản ra để tránh bị nhầm lẫn giữa các thư viện python.
  - Cài các packages ở trong `requirements.txt`

7. Cài đặt `virtualenv`
    - Trở về `/home/ec2-user`

```
pip3.9 install virtualenv
```

![install-virtualenv](/images/3-ecs-alb-and-api-gateway/3.2-install-virtualenv.png)

8. Vào lại thư mục `cdk-workshop-02`
    - Cài đặt một trường ảo trong thư mục `venv`
    - Kích hoạt một trường ảo trong `venv/bin/activate`

```
virtualenv venv
source venv/bin/activate
```

![setup-venv](/images/3-ecs-alb-and-api-gateway/3.3-setup-venv.png)

9. Cài các thư viện cần ở trong `requirements.txt`

```
pip install -r requirements.txt
```

![install-required-packges](/images/3-ecs-alb-and-api-gateway/3.4-install-required-packges.png)

#### Triển khai

10. Kiểm tra lại những gì chúng ta đã tạo trước khi triển khai

```
cdk diff
```

![check-resources](/images/3-ecs-alb-and-api-gateway/3.5-check-resources.png)

11. Trước khi triển khai, thì chúng ta cần phải khởi động môi trường máy chủ EC2

```
cdk boostrap
```

![boostrap-environment](/images/3-ecs-alb-and-api-gateway/3.6-boostrap-environment.png)

12. Khi đó thì chúng ta triển khai stack đã khai báo

```
cdk deploy
```

![start-deploy](/images/3-ecs-alb-and-api-gateway/3.7-start-deploy.png)

Nhấn `y` để tiếp tục

13. Kết thúc và kiểm tra lại kết quả

![deploy-done](/images/3-ecs-alb-and-api-gateway/3.8-deploy-done.png)

Truy cập vào đường link của API gateway (`ProxyToLBECSEndpoint`), thêm path `/ecs`. Bạn sẽ truy cập được vào web server được triển khai trong ECS.

Để kiểm tra sự hoạt động của Application Load Balancer, nhấn làm mới trình duyệt một vài lần, bạn sẽ thấy đích đến của trang web thay đổi. Ở phần trước, chúng ta đặt tham số desired_count=3, nên sẽ có 3 container được triển khai song song với nhau đằng sau ALB. Bạn sẽ thấy sự thay đổi ở Server address qua các lần làm mới trình duyệt.

![check-ecs](/images/3-ecs-alb-and-api-gateway/3.9-check-ecs.png)

Nếu bạn đã chạy được đến phần này, chúc mừng bạn đã hoàn thành phần đầu tiên của bài lab CDK cơ bản 2. Ở phần tiếp theo, chúng ta sẽ sử dụng CDK để triển khai một hàm Lambda đằng sau 1 resource của API Gateway.