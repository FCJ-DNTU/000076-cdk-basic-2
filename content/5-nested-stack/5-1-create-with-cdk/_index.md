+++
title = "Create nested stacks with CDK"
date = 2024
weight = 1
chapter = false
pre = "<b>5.1 </b>"
+++

{{% notice note %}}
To create a maintainable, reusable and readable codebase of CDK, we can split the definition of stack in `cdk_workshop_02_stack.py` into multiple file and include in a main file to deploy stack.
{{% /notice %}}

#### Create nested stacks with CDK

1. Firstly, create `cdk_workshop_02/main_stack.py` files for the root stack.

```
touch cdk_workshop_02/main_stack.py
touch cdk_workshop_02/lb_fargate_stack.py
touch cdk_workshop_02/lambda_stack.py.py
touch cdk_workshop_02/api_gateway_stack.py.py
```

![create-stack-definition-files](/images/5-nested-stack/5.1-create-stack-definition-files.png)

2. Declare a nested stack for **ECS Fargate** + **Application Load Balancer** with the file `cdk_workshop_02/lb_fargate_stack.py`

```py
from aws_cdk import (
    NestedStack,
    aws_ecs as ecs,
    aws_ecs_patterns as ecsp,
)
from constructs import Construct

class LBFargateStack(NestedStack):

    def __init__(self, scope: Construct, **kwargs) -> None:
        super().__init__(scope, "LB Fargate Stack", **kwargs)

        # Declare a Load Balancer Fargate 
        lb_fargate_service = ecsp.ApplicationLoadBalancedFargateService(
            self, 
            "MyWebServer",
            task_image_options=ecsp.ApplicationLoadBalancedTaskImageOptions(
                image=ecs.ContainerImage.from_registry("nginxdemos/hello")),
            public_load_balancer=True,
            desired_count=3
        )
        
        self.lb = lb_fargate_service.load_balancer
```

3. Create a nested stack for **Lambda** in `cdk_workshop_02/lambda_stack.py`.

```py
from aws_cdk import (
    NestedStack,
    aws_s3 as s3,
    aws_lambda as lambda_
)
from constructs import Construct

class LambdaStack(NestedStack):

    def __init__(self, scope: Construct, **kwargs) -> None:
        super().__init__(scope, "Lambda Stack", **kwargs)

        # Add S3 bucket
        bucket = s3.Bucket(self, "WidgetStore")
        
        # Add Lambda function
        handler = lambda_.Function(self, "WidgetHandler",
                    runtime=lambda_.Runtime.NODEJS_18_X,
                    code=lambda_.Code.from_asset("resources"),
                    handler="widget.main",
                    environment=dict(
                    BUCKET=bucket.bucket_name)
                    )
        
        # Grant bucket permission to lambda function
        bucket.grant_read_write(handler)
        
        self.handler = handler
```

4. Create a nested stack for **API Gateway** at `cdk_workshop_02/api_gateway_stack.py`.

```py
from aws_cdk import (
    NestedStack,
    aws_apigateway as apigateway,
    aws_ecs_patterns as ecsp,
    aws_lambda as lambda_
)
from constructs import Construct

class APIGatewayStack(NestedStack):

    def __init__(self, scope: Construct, lb_dns: str, lambda_handler: lambda_.Function,  **kwargs) -> None:
        super().__init__(scope, "API Gateway Stack", **kwargs)

        # Define API Gateway
        api = apigateway.RestApi(self, "ProxyToLBECS")

        # Add resource and method for ECS proxy request
        ecs_proxy = api.root.add_resource("ecs")
        ecs_proxy.add_method("GET", apigateway.HttpIntegration(f"http://{lb_dns}"))

        # Create Lambda intergration
        get_widgets_integration = apigateway.LambdaIntegration(lambda_handler,
                request_templates={"application/json": '{ "statusCode": "200" }'})
        
        # Add resource and method for Lambda proxy request
        lambda_proxy = api.root.add_resource("lambda")
        lambda_proxy.add_method("GET", get_widgets_integration)
        
        self.url = api.url
```

5. Put everything together in the root stack at `cdk_workshop_02/main_stack.py`.

```py
from aws_cdk import (
    Stack,
    aws_apigateway as apigateway,
    aws_s3 as s3,
    aws_lambda as lambda_,
    CfnOutput
)
from constructs import Construct
from .lambda_stack import LambdaStack
from .lb_fargate_stack import LBFargateStack
from .api_gateway_stack import APIGatewayStack

class MainStack(Stack):

    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)
        
        lb_fargate_stack = LBFargateStack(self)
        
        lambda_stack = LambdaStack(self)
        
        # Get the DNS value of the Application Load Balancer 
        lb_dns = lb_fargate_stack.lb.load_balancer_dns_name
        
        api_gateway_stack = APIGatewayStack(self, lb_dns, lambda_stack.handler)
        
        # Define stack output
        CfnOutput(self, "API Gateway URL", value=api_gateway_stack.url)
```

6. Change the code in `app.py` to use our new template.

```py
#!/usr/bin/env python3
import os

import aws_cdk as cdk

from cdk_workshop_02.main_stack import MainStack


app = cdk.App()
# CdkWorkshop02Stack(app, "CdkWorkshop02Stack")
MainStack(app, "MainStack")

app.synth()
```

7. Deploy!!!

```
cdk deploy
```

![deploy-done](/images/5-nested-stack/5.2-deploy-done.png)

After deploying successfully, you can see the newly created stacks in the AWS **CloudFormation** Console.

![check-main-stack](/images/5-nested-stack/5.3-check-main-stack.png)

You can try the **API Gateway** endpoint for **Lambda** and **ECS** like in the previous section.

![check-ecs](/images/5-nested-stack/5.5-check-ecs.png)

Upload files to **S3** for testing

![upload-files](/images/5-nested-stack/5.4-upload-files.png)
![check-lambda-s3](/images/5-nested-stack/5.6-check-lambda-s3.png)

{{% notice note %}}
Changing the code inside the `app.py` file will create a new **CloudFormation** template. In this advance workshop, each **Cloudformation** stack needs 2 elastic IPs to function properly. When deploying a stack, if the number of Elastic IPs exceeds the limitation of your account (default is 5), the **CloudFormation** stack may not initiate and get stuck at the IN_PROGRESS state. Make sure that you release all the unneeded Elastic IPs when performing this workshop.
{{% /notice %}}