## How-To Video

[![Watch the video](https://img.youtube.com/vi/w-vQmbkuIig/maxresdefault.jpg)](https://youtu.be/w-vQmbkuIig)

## Prerequisites

None

## Steps

### 1. Create the CloudFormation stack:

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
Usage
```
aws cloudformation create-stack --stack-name <my-stack-name> --template-body file://managed-config-rules.json --parameters file://parameters.json
```
Example
```
aws cloudformation create-stack --stack-name managed-config-rules --template-body file://managed-config-rules.json --parameters file://parameters.json
```

### 2. Modify resources and observe changes to compliance status
