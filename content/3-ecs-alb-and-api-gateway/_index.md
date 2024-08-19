+++
title = "ECS, ALB and API Gateway"
date = 2024
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

#### Create ECS Cluster and Application Load Balancer

1. Access the VSCode Workspace
    - Create a new directory named `cdk-workshop-02`
    - Enter `cdk-workshop-02`
    - Init new `python` CDK project

![create-work-folder](/images/3-ecs-alb-and-api-gateway/3.1-create-work-folder.png)

2. Go to file `cdk-workshop-02/cdk_workshop_02_stack.py`, import these libraries

```
from aws_cdk import (
  Stack,
  aws_ecs as ecs,
  aws_ecs_patterns as ecsp,
  aws_apigateway as apigateway
)
from constructs import Construct
```

3. In `cdk-workshop-02/cdk_workshop_02_stack.py`, declare a ECS cluster together with Application Load Balancer in the `__init__()` function

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
`ecsp.ApplicationLoadBalancedFargateService` will create the following resources:

  - A new VPC with default configuration (subnet, Internet Gateway, RouteTable, EIP, Nat Gateway, …)
  - A ECS cluster in the newly created VPC, running on Fargate
  - An ECS Task defined by the `nginxdemos/hello` image from Dockerhub
  - A ECS container group initiated from the ECS Task, with the number of instances is 3
  - A Application Load Balancer fronting the ECS cluster
  - Needed roles and security groups

#### Configure API Gateway

4. Declare an **API Gateway**

```py
# Define API Gateway
api = apigateway.RestApi(self, "ProxyToLBECS")
```

5. Configure **API Gateway** resource to point to the DNS of the **Application Load Balancer**

```py
# Get the DNS value of the Application Load Balancer 
lb = lb_fargate_service.load_balancer
lb_dns = lb_fargate_service.load_balancer.load_balancer_dns_name

# Add resource and method for proxy request
proxy = api.root.add_resource("ecs")
proxy.add_method("GET", apigateway.HttpIntegration(f"http://{lb_dns}"))
```

You can read more about the methods and properties of ApplicationLoadBalancedFargateService at [the official CDK documentation of AWS (Python)](https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_ecs_patterns/ApplicationLoadBalancedFargateService.html)

6. Check the code in `cdk-workshop-02/cdk_workshop_02_stack.py`

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

#### Setup virtual environment for python
Before we deploy, we must
  - Setup a virtual environment in `cdk-workshop-02` directory, because our EC2 instance has 2 versions of python, we have to isolate them to make sure there aren't conflicts between python's packages.
  - Install packages in `requirements.txt`

1. Install `virtualenv`
    - Back to `/home/ec2-user`

```
pip3.9 install virtualenv
```

![install-virtualenv](/images/3-ecs-alb-and-api-gateway/3.2-install-virtualenv.png)

8. Change directory back to `cdk-workshop-02`
    - Setup a virtual environment in `venv`
    - Active virtual environment in to `venv/bin/activate`

```
virtualenv venv
source venv/bin/activate
```

![setup-venv](/images/3-ecs-alb-and-api-gateway/3.3-setup-venv.png)

9. Install all required packages in `requirements.txt`

```
pip install -r requirements.txt
```

![install-required-packges](/images/3-ecs-alb-and-api-gateway/3.4-install-required-packges.png)

#### Deploy

10. Verify the resources that would be created by running

```
cdk diff
```

![check-resources](/images/3-ecs-alb-and-api-gateway/3.5-check-resources.png)

11. Before we deploy this stack, we have to boostrap our environment

```
cdk boostrap
```

![boostrap-environment](/images/3-ecs-alb-and-api-gateway/3.6-boostrap-environment.png)

12. Then we deploy the stack

```
cdk deploy
```

![start-deploy](/images/3-ecs-alb-and-api-gateway/3.7-start-deploy.png)

Choose `y` to continue

13. Finish and test result

![deploy-done](/images/3-ecs-alb-and-api-gateway/3.8-deploy-done.png)

Access the link to the API Gateway (`ProxyToLBECSEndpoint`), add the path `/ecs`. You will be able to access the webserver deployed in ECS.

To verify that the ALB works properly, hit refresh a couple of times, and you will see the target of the website change. In the previous section, we set the parameter `desired_count=3`, so there will be 3 containers being deployed in parallel behind the ALB. You will notice the change in the field `Server address` between the refreshes.

![check-ecs](/images/3-ecs-alb-and-api-gateway/3.9-check-ecs.png)

If you have reached this state, congratulations on finishing the first part of the CDK basic 2 Workshop. In the next section, we will use CDK to deploy a Lambda function behind an API Gateway resource.