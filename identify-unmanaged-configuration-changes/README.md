## Prerequisites

1. CloudFormation templates (identify-unmanaged-configuration-changes-infrastructure.json) is uploaded to S3 bucket

## Usage

Example parameters.json:
```
[
  {
    "ParameterKey": "pInfrastructureStackTemplate",
    "ParameterValue": "https://my-bucket.s3-us-gov-west-1.amazonaws.com/identify-unmanaged-configuration-changes-infrastructure.json"
  },
  {
    "ParameterKey": "pNotificationEmail",
    "ParameterValue": "me@example.com"
  },
  {
    "ParameterKey": "pAllowedBastionCidrBlock",
    "ParameterValue": "1.2.3.4/32"
  }
]
```
Create the CloudFormation stack:
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://identify-unmanaged-configuration-changes-toplevel.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM
```

Confirm the email subscription to the SNS topic

## Test Steps

### 1. Confirm subscription to the SNS Topic using the link provided via email

### 2. Using the EC2 console, change the security group created by CloudFormation

For example, change the inbound-ssh rule from 1.2.3.4/32 to 5.6.7.8/32

### 3. Using the Config console, confirm the config rule ending in verify-security-groups-cfn evaluated the security group as NON_COMPLIANT

### 4. Confirm email notification was received of the non-compliant config rule

### 5. Using the EC2 console, revert the security group to its original value

For example, change the inbound-ssh rule from 5.6.7.8/32 to 1.2.3.4/32

### 6. Using the Config console, confirm the config rule ending in verify-security-groups-cfn evaluated the security group as COMPLIANT

### 7. Using the CloudFormation console, update the pAllowedBastionCidrBlock parameter

For example, change the pAllowedBastionCidrBlock parameter from 1.2.3.4/32 to 9.10.11.12/32 

### 8. Using the Config console, confirm the config rule ending in verify-security-groups-cfn evaluated the security group as COMPLIANT

### 9. Confirm email notification was received of the compliant config rule

## Notes

This solution does not contain all the features of drift detection, it simply identifies configuration changes to security groups made outside of CloudFormation.

There are known test cases for which this solution does not have feature parity with drift detection. For example, in Test Step 1, if a security group rule is added instead of modified, the config rule will properly be evaluated as NON_COMPLIANT, but the added rule will persist after updating the stack in Test Step 3 and the config rule will be evaluated as COMPLIANT.
