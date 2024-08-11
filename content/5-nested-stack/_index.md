+++
title = "Nested Stack"
date = 2024
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

#### Nested Stack
It is infeasible to deploy a large architecture in a single file. Since CDK works on CloudFormation, we can utilize nested stacks to split our big stack into different smaller, easier to manage sub stacks.

In this last section of the CDK Advance Workshop, we will split the template in the previous part into smaller templates with separate responsibilities. In particular, we will split it into 3 templates:

  1. ECS and ALB
  2. Lambda and S3
  3. API Gateway

![CDKAdvanceArcg](/images/1-introduction/CDKAdvanceArch.png)