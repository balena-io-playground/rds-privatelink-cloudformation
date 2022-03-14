# RDS Privatelink CloudFormation Template

This creates a CloudFormation stack that provisions a VPC Service Endpoint and
Network Load Balancer. The stack allows an RDS database to be consumed as a
service from an authorized IAM user or role of another AWS account.

## Deployment

This CloudFormation template can be deployed using [AWS CLI][AWS CLI].
Make sure that the AWS CLI is [configured][AWS CLI Config] to deploy the stack to the target
AWS account.  Replace the values accordingly before executing the command.

```bash
    aws cloudformation deploy \
      --stack-name RDS-Privatelink-Stack \
      --template-file cloudformation.yml \
      --capabilities CAPABILITY_NAMED_IAM \
      --parameter-overrides \
      VPCEndpointServiceAllowedPrincipals=arn:aws:iam::123456789:serviceconsumer \
      RdsIpAddress=10.0.0.10 \
      SubnetList=subnet-12345678,subnet-23456789,subnet-34567890 \
      VpcId=vpc-12345678 \
      --no-execute-changeset
```

[AWS CLI]: https://aws.amazon.com/cli/
[AWS CLI Config]: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
