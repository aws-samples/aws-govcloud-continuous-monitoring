## How-To Video

[![Watch the video](https://img.youtube.com/vi/0rymMbEYNiY/maxresdefault.jpg)](https://youtu.be/0rymMbEYNiY)

## Prerequisites

None

## Usage

```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://alert-on-manual-tag-modifications.json --parameters ParameterKey=pProtectedTags,ParameterValue=<my-protected-tags> ParameterKey=pEmailSubscription,ParameterValue=<my-email> --capabilities CAPABILITY_NAMED_IAM
```
Example
```
aws cloudformation create-stack --stack-name alert-on-manual-tag-modifications --template-body file://alert-on-manual-tag-modifications.json --parameters ParameterKey=pProtectedTags,ParameterValue="Environment\, Hostname\, OS" ParameterKey=pEmailSubscription,ParameterValue=me@example.com --capabilities CAPABILITY_NAMED_IAM
```
## Test Steps

### 1. Confirm subscription to the SNS Topic using the link provided via email

### 2. Create a resource with Protected Tag "Environment" via CloudFormation
Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://vpc.template
```
Example
```
aws cloudformation create-stack --stack-name vpc --template-body file://vpc.template
```
### 3. Modify the Protected Tag "Environment" outside of CloudFormation

### 4. Confirm an email is received identifying the Protected Tag and the user who modified it
