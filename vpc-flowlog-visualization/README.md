# Visualize VPC Flow Logs in Kibana
This solution enables VPC flow logs for a given VPC and pushes them to ElasticSearch.
Kibana is then used to visualize the injested VPC flow logs

## How-To Video

[![Watch the video](https://img.youtube.com/vi/j4UpXDhqK6M/maxresdefault.jpg)](https://youtu.be/j4UpXDhqK6M)

## Prerequisites
An existing VPC

### Flowlogs to Elasticsearch Lambda Function

A Lambda Deployment packaged needs to be created and upload to S3

See: https://docs.aws.amazon.com/lambda/latest/dg/nodejs-create-deployment-pkg.html

```
zip flowlog-to-es.zip vpc-flowlogs-to-es.js
aws s3 cp flowlog-to-es.zip s3://<bucket>/vpc-flowlog-visualization/flowlog-to-es.zip
```

## Kibana Configuration

The first time you access Kibana, you will be asked to set the defaultindex.

To set the defaultindex in the Amazon ES cluster:

1. Set the Index name or pattern to cwl-*.
2. For Time-field name, type @timestamp.
3. Choose Create.
