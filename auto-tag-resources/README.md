## Prerequisites

1. Lambda function (auto-tag-resources.zip) is uploaded to S3 bucket

## Test Steps

### 1. Create the CloudFormation stack

Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://auto-tag-resource.json --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=pSupportingFilesBucket,ParameterValue=<my-bucket> ParameterKey=pSupportingFilesPrefix,ParameterValue=<my-prefix>
```
Example
```
aws cloudformation create-stack --stack-name auto-tag-resources --template-body file://auto-tag-resources.json --capabilities CAPABILITY_NAMED_IAM  --parameters ParameterKey=pSupportingFilesBucket,ParameterValue=my-s3-bucket ParameterKey=pSupportingFilesPrefix,ParameterValue=my-s3-prefix/
```
### 2. Find an AMI

Usage
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --region <my-aws-region> --query Parameters[0].[Value] --profile <my-profile>
```
Example
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --region us-gov-west-1 --query Parameters[0].[Value] --profile child
[
    "ami-6aa7db0b"
]
```
### 3. Create an EC2 instance without an "Owner" tag with the AMI found in step 2

Usage
```
aws ec2 run-instances --image-id <my-ami-id> --count 1 --instance-type <my-instance-type>
```
Example
```
aws ec2 run-instances --image-id ami-6aa7db0b --count 1 --instance-type m4.large 
```
### 3. Confirm the EC2 instance has an "Owner" tag
