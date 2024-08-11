+++
title = "Clean up resources"
date = 2024
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

#### Clean up resources
Similar to the CDK Basic Workshop, you can clean up all the resources in order to avoid unwanted charges. To delete all the resources in the stack, run the command.

```
cdk destroy
```

Or you can destroy it manually

![delete-manual](/images/6-clean-up-resources/6.1-delete-manual.png)

If you have uploaded files into the bucket, it cannot be destroyed by the cdk destroy command. You will need to manually delete all the files inside the bucket, and then delete the bucket.

Congratulations on finishing the CDK Advance Workshop!