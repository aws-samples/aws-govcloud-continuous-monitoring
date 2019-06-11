## Prerequisites

1. Systems Manager Parameter Store Parameter specified in CloudFormation parameter `pJiraPasswordParameterName` contains the Jira password for the user specified in the CloudFormation parameter `pJiraUsername`

2. [Default Encryption for New EBS Volumes](https://aws.amazon.com/blogs/aws/new-opt-in-to-default-encryption-for-new-ebs-volumes/) is NOT turned on

## Usage

Example parameters.json:
```
[
  {
    "ParameterKey": "pSupportingFilesPrefix",
    "ParameterValue": "my-s3-prefix/"
  },
  {
    "ParameterKey": "pSupportingFilesBucket",
    "ParameterValue": "my-s3-bucket"
  },
  {
    "ParameterKey": "pJiraUsername",
    "ParameterValue": "my-jira-user"
  },
  {
    "ParameterKey": "pJiraUrl",
    "ParameterValue": "https://my-project.atlassian.net"
  },
  {
    "ParameterKey": "pJiraProjectKey",
    "ParameterValue": "ABC"
  },
  {
    "ParameterKey": "pJiraPasswordParameterName",
    "ParameterValue": "jira-password"
  },
  {
    "ParameterKey": "pJiraIssueTypeName",
    "ParameterValue": "Story"
  }
]
```
Create the CloudFormation stack:
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://create-jira-issues.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM
```

## Test Steps

### 1. Find an unencrypted AMI

Usage
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --region <my-aws-region> --query Parameters[0].[Value]
```
Example
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --region us-gov-west-1 --query Parameters[0].[Value]
[
    "ami-6aa7db0b"
]
```
### 2. Create an EC2 instance with an unencrypted EBS Volume from the AMI found in step 1

Usage
```
aws ec2 run-instances --image-id <my-ami-id> --count 1 --instance-type <my-instance-type>
```
Example
```
aws ec2 run-instances --image-id ami-6aa7db0b --count 1 --instance-type m4.large
```

### 3. Open Jira and confirm an issue was created

> EBS Volume vol-123 is unencrypted
> 
> User my-user created instance i-123 in account 123456789012 with unencrypted volume vol-123
> 
> More information about encrypting EBS Volumes: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html

