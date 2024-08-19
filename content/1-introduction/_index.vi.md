+++
title = "Giới thiệu"
date = 2024
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

#### Tổng quan
Bạn nên thực hành bài workshop CDK Basic 2 trước khi thực hành tiếp bài này, bạn có thể thông qua đường dẫn [CDK Basic Workshop](https://000038.awsstudygroup.com/) để biết thêm về CDK. Bạn cũng nên tìm hiểu qua để có một số kiến thức về API Gateway, Elastic Load Balancer, Elastic Container Service, và Lambda.

Chúng tôi sẽ dùng lại các cấu hình ở trong bài CDK Basic trước đó để thực hành bài này. `CDK 2.151.0` sẽ được sử dụng. Sau khi thực hành bài này xong thì bạn sẽ nằm được cách để
- Sử dụng CDK để tạo ra một kiến trúc ứng dụng đơn giản sử dụng API Gateway, Elastic Load Balancer, Elastic Container Service, và Lambda.
- Sử dụng các "nested stack" với CDK

Đây là mô hình kiến trúc mà CDK sẽ xây dựng:
![CDKAdvanceArcg](/images/1-introduction/CDKAdvanceArch.png)

#### Content

1. [Introduction](/1-introduction)
2. [Preparation](/2-preparation)
3. [API Gateway and ECS](/3-ecs-alb-and-api-gateway)
4. [Lambda and S3](/4-lambda-and-s3)
5. [Nested stack](/5-nested-stack)
6. [Clean up](/6-clean-up-resources)