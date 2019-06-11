## Prerequisites

1. Configuration profiles are configured for "parent" and "child"
```
aws configure --profile parent
aws configure --profile child
```
2. CloudFormation template (main.template) is uploaded to S3 bucket
3. Bucket Policy on S3 bucket containing CloudFormation templates allows s3:GetObject permissions to child account. For example:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws-us-gov:iam::<child-account-number>:root"
            },
            "Action": "s3:Get*",
            "Resource": "arn:aws-us-gov:s3:::<bucket-containing-cloudformation-templates>/*"
        }
    ]
}
```

## Test Steps

### 1. Create a role for Systems Manager in the parent account

Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://AWS-SystemManager-AutomationAdministrationRole.json --capabilities CAPABILITY_NAMED_IAM --profile parent
```
Example
```
aws cloudformation create-stack --stack-name systems-manager-role --template-body file://AWS-SystemManager-AutomationAdministrationRole.json --capabilities CAPABILITY_NAMED_IAM --profile parent
```
### 2. Allow the parent account access to the child account

Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://allow-parent-access.json --parameters ParameterKey=pParentAccountNumber,ParameterValue=<Acccount ID of Parent> --capabilities CAPABILITY_NAMED_IAM --profile child
```
Example
```
aws cloudformation create-stack --stack-name allow-parent-access --template-body file://allow-parent-access.json --parameters ParameterKey=pParentAccountNumber,ParameterValue=123456789012 --capabilities CAPABILITY_NAMED_IAM --profile child
```
### 3. Create the Systems Manager Automation Document in the parent account

Usage
```
aws ssm create-document --content file://ssm-document-create-child-vpc.json --name <my-automation-document> --document-type Automation --profile parent
```
Example
```
aws ssm create-document --content file://ssm-document-create-child-vpc.json --name create-child-vpc --document-type Automation --profile parent
```
### 4. Find an AMI in the child account

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
### 5. Create a tagged EC2 instance from the AMI found in step 4

Usage
```
aws ec2 run-instances --image-id <my-ami-id> --count 1 --instance-type <my-instance-type> --tag-specifications ResourceType=instance,Tags=[{Key=<my-key>,Value=<my-value>}] --profile <my-profile>
```
Example
```
aws ec2 run-instances --image-id ami-6aa7db0b --count 1 --instance-type m4.large --tag-specifications ResourceType=instance,Tags=[{Key=MyKey,Value=MyValue}] --profile child
```
### 6. Start the execution of the Automation document in the parent account

Usage
```
aws ssm start-automation-execution --document-name <my-document-name> create-child-vpc --targets Key=tag:<my-key>,Values=<my-value> --target-parameter-name Dummy --target-locations Accounts=<my-child-account-id>,Regions=<my-region> --parameters pS3Bucket=<my-bucket-containing-cloudformation-templates>,pVpcName=<my-vpc-name>,pVpcCidr=<my-vpc-cidr>,pS3KeyPrefix=<my-key-prefix> --profile <my-profile>
```
Example
```
aws ssm start-automation-execution --document-name create-child-vpc --targets Key=tag:MyKey,Values=MyValue --target-parameter-name Dummy --target-locations Accounts=123456789012,Regions=us-gov-west-1 --parameters pS3Bucket=my-continuous-monitoring-bucket,pVpcName=production,pVpcCidr='10.0.0.0/24',pS3KeyPrefix='multi-account-automation-document/' --profile parent
```
### 7. Confirm a new VPC is created in the child account
