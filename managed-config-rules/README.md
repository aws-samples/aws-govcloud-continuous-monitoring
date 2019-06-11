## Prerequisites

None

## Usage

Example parameters.json:
```
[
  {
    "ParameterKey": "amiIds",
    "ParameterValue": "ami-6aa7db0b"
  },
  {
    "ParameterKey": "blacklistedApplicationNames",
    "ParameterValue": "firefox"
  },
  {
    "ParameterKey": "requiredApplicationNames",
    "ParameterValue": "chrome"
  },
  {
    "ParameterKey": "tag1Key",
    "ParameterValue": "Environment"
  }
]
```
Create the CloudFormation stack:
Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://managed-config-rules.json --parameters file://parameters.json
```
Usage
```
aws cloudformation create-stack --stack-name managed-config-rules --template-body file://managed-config-rules.json --parameters file://parameters.json
```

## Test Steps

### 1. Modify resources and observe changes to compliance status
