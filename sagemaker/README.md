## Creating SageMaker Studio Domains using CloudFormation

Amazon SageMaker Studio (https://docs.aws.amazon.com/sagemaker/latest/dg/gs-studio.html) is the first fully integrated development environment (IDE) for machine learning (ML). It provides a single, web-based visual interface where you can perform all ML development steps required to build, train, tune, debug, deploy, and monitor models. AWS CloudFormation (https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) gives you an easy way to model a collection of related AWS and third-party resources, provision them quickly and consistently, and manage them throughout their lifecycle, by treating infrastructure as code.

In this blog post, we want to demonstrate how you can create a SageMaker Studio domain using CloudFormation.

## How to run from Management Console

Please make sure you don't have any existing Studio Domains. Because at the time of writing, an AWS account is limited to one domain per region. Creating extra domain will cause error.

### Step 1
Zip `UserProfile_function.py`, `domain_function.py`, and `cfnresponse.py` and upload the zip files to your S3 bucket. The name of the zip files must be `UserProfile_function.zip`, `domain_function.zip`, and `cfnresponse.zip`.

### Step 2
In your AWS Management Console, select CloudFormation, then click `Create stack` and `With new resources (standard)`. You can choose `Upload a template file` under `Specify template`, then click `Choose file` to upload `sagemaker_studio_template.yaml`. Once the template is uploaded, click `Next`.

Make sure in `Specify stack details` page, you enter the right `S3Bucket`, `SubnetIds`, `UserProfileName`, and `VPCId`. The S3 bucket should contain the 3 zip files from Step 1.

### Step 3
Leave the rest options as default and click `Create stack`.

The whole process should take a few minutes.

## How to run from command line

`aws cloudformation deploy --stack-name <stack-name> --template-file sagemaker_studio_template.yaml --capabilities CAPABILITY_IAM --parameter-overrides S3Bucket=<bucketName> VPCId=<VpcId> SubnetIds=<Comma-Seperated SubnetIds>`

## Note for new account ONLY

Please note that if you are running this template in a brand new account (ONLY for new accounts), you might want to add `CreateServiceLinkedRole` IAM permission to create  `AWSServiceRoleForAmazonSageMakerNotebooks` service linked role in `LambdaExecutionPolicy` IAM policy. One way of doing so is adding the following code after line 69 of `sagemaker_studio_template.yaml`.

```
  - Sid: ServiceLinkedRole
     Effect: Allow
     Action:
       - iam:CreateServiceLinkedRole
     Resource:
       - !Sub "arn:${AWS::Partition}:iam::${AWS::AccountId}:role/aws-service-role/sagemaker.amazonaws.com/AWSServiceRoleForAmazonSageMakerNotebooks"
```
**Many thanks to my colleague Dave Dickerson for his several great feedbacks!**

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
