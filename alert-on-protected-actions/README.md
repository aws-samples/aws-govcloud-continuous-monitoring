## How-To Video

[![Watch the video](https://img.youtube.com/vi/9OLB4d9Kctk/maxresdefault.jpg)](https://youtu.be/9OLB4d9Kctk)

## Prerequisites

None

## Steps

### 1. Create the CloudFormation stack

Example parameters.json:
```
[
  {
    "ParameterKey": "pEvents",
    "ParameterValue": "StopLogging, CreateNetworkAclEntry, CreateRoute, AuthorizeSecurityGroupEgress, AuthorizeSecurityGroupIngress, AttachInternetGateway, AttachVpnGateway, CreateVpcPeeringConnection, DeleteNetworkAcl, AttachRolePolicy, CreateUser, CreateVirtualMFADevice, DeactivateMFADevice, CreateAccessKey, CreateSAMLProvider, UpdateSAMLProvider, DeleteBucketPolicy, PutBucketAcl, PutBucketCors, PutBucketPolicy"
  },
  {
    "ParameterKey": "pEmailSubscription",
    "ParameterValue": "me@example.com"
  }
]
```

Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://alert-on-protected-actions.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM
```
Example
```
aws cloudformation create-stack --stack-name alert-on-protected-actions --template-body file://alert-on-protected-actions.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM
```

### 2. Confirm subscription to the SNS Topic using the link provided via email

### 3. Execute one of the protected actions

### 4. Confirm an email is received identifying the protected action and the user who executed it
