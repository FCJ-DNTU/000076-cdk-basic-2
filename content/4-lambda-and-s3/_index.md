+++
title = "Lambda and S3"
date = 2024
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

#### Lambda and S3
In this section, we will configure a Lambda function with API Gateway. We will reuse the template of the previous section (API Gateway and ECS)

1. Create the `resources` directory at the project root

```
mkdir resources
```

![create-resource](/images/4-lambda-and-s3/4.1-create-resource.png)

2. Create the `widget.js` file in the `resources` directory (use to create lambda)

```js
/* 
This code uses callbacks to handle asynchronous function responses.
It currently demonstrates using an async-await pattern. 
AWS supports both the async-await and promises patterns.
For more information, see the following: 
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/calling-services-asynchronously.html
https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html 
*/
const { S3Client, ListObjectsV2Command } = require("@aws-sdk/client-s3");

exports.main = async function(event, context) {
  var method = event.httpMethod;
  const bucketName = process.env.BUCKET; // Ensure this is correctly read
    if (!bucketName) {
      console.error('No bucket name provided in environment variables.');
      return {
        statusCode: 500,
        body: 'No bucket name provided in environment variables.'
      };
    }

    const s3 = new S3Client();

    const params = {
      Bucket: bucketName
    };

    try {
      // Send the ListObjectsV2Command to S3
      const data = await s3.send(new ListObjectsV2Command(params));

      // Extract object keys if Contents exists
      const objectKeys = data.Contents ? data.Contents.map(obj => obj.Key) : [];

      // Return a response
      return {
        statusCode: 200,
        body: JSON.stringify({ "objectKeys": objectKeys })
      };
    } catch (err) {
      // Log the detailed error for troubleshooting
      console.error('Error listing objects:', JSON.stringify(err, null, 2));
      return {
        statusCode: 500,
        body: `Error listing objects: ${err.message}`
      };
    }
}

```

This lambda function will return the list of objects in a S3 bucket.

3. In `cdk_workshop_02/cdk_workshop_02_stack.py`, declare a S3 bucket

```py
# Add S3 bucket
bucket = s3.Bucket(self, "WidgetStore")
```

4. Create the **Lambda function** and grant the S3 bucket read and write permissions

```py
# Add Lambda function
handler = lambda_.Function(self, "WidgetHandler",
  runtime=lambda_.Runtime.NODEJS_18_X,
  code=lambda_.Code.from_asset("resources"),
  handler="widget.main",
  environment=dict(BUCKET=bucket.bucket_name)
)
        
# Grant bucket permission to lambda function
bucket.grant_read_write(handler)
```

5. Create an **API Gateway** intergration

```py
# Create intergration
get_widgets_integration = apigateway.LambdaIntegration(
	handler,
	request_templates={"application/json": '{ "statusCode": "200" }'}
)
```

6. Add a new resource and a new method to the **API Gateway**

```py
# Add resource and method for proxy request
lambda_proxy = api.root.add_resource("lambda")
lambda_proxy.add_method("GET", get_widgets_integration)
```

7. Don’t forget to import the needed libraries to use **Lambda**, **API Gateway** and **S3**.

After importing, recheck your `cdk_workshop_02/cdk_workshop_02_stack.py` file.

```py
from aws_cdk import (
    Stack,
    aws_ecs as ecs,
    aws_ecs_patterns as ecsp,
    aws_apigateway as apigateway,
    aws_s3 as s3,
    aws_lambda as lambda_
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
        
        # Add S3 bucket
        bucket = s3.Bucket(self, "WidgetStore")
        
        # Add Lambda function
        handler = lambda_.Function(self, "WidgetHandler",
        		runtime=lambda_.Runtime.NODEJS_18_X,
            code=lambda_.Code.from_asset("resources"),
            handler="widget.main",
            environment=dict(BUCKET=bucket.bucket_name)
        )
                
        # Grant bucket permission to lambda function
        bucket.grant_read_write(handler)
        
        # Create intergration
        get_widgets_integration = apigateway.LambdaIntegration(
        	handler,
        	request_templates={"application/json": '{ "statusCode": "200" }'}
        )
        
        # Add resource and method for proxy request
        lambda_proxy = api.root.add_resource("lambda")
        lambda_proxy.add_method("GET", get_widgets_integration)

```

8. Deploy the stack

```
cdk deploy
```

![deploy-done](/images/4-lambda-and-s3/4.2-deploy-done.png)

9. Upload file to **S3 Bucket**

Access the API Gateway endpoint, and add the path `/labmda`. You will see the result returned from the Lambda function.

To verify that the Lambda function is working properly, access the S3 Console. Access the newly created bucket and upload a random file.

![open-s3](/images/4-lambda-and-s3/4.3-open-s3.png)
![upload-file](/images/4-lambda-and-s3/4.4-upload-file.png)
![confirm-upload](/images/4-lambda-and-s3/4.5-confirm-upload.png)

10. Check result

Access the endponit to the Lambda function `xxxxxxx.execute-api.[REGION].amazonaws.com/prod/lambda` of the API Gateway. You will see the new file name being displayed.

![check-result](/images/4-lambda-and-s3/4.6-check-result.png)

If you have reached this part, congratulations on finishing the second part of the CDK Advance Workshop. In the next section, we will refractor the project by splitting the components in the file `cdk_workshop_02/cdk_workshop_02_stack.py` into different files, and use nested stack to deploy the architecture.