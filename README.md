# RDS Privatelink CloudFormation Template

This creates a CloudFormation stack that provisions a VPC Service Endpoint and
Network Load Balancer. The stack allows an RDS database to be consumed as a
service from an authorized IAM user or role of another AWS account.

This stack includes a lambda function to automatically resolve the IP address
of the RDS endpoint.  This IP address is then set to the network load balancer's
target group.  The lambda function keeps track of the IP addresses resolved from
the RDS endpoint's FQDN.  If an IP address is missing from the last 3 domain
name resolution done by the lambda function, it is removed from the NLB target
group.

This stack is based on the [Hostname-as-Target for Network Load Balancers solution][Hostname-as-Target for Network Load Balancers] 
recommended by AWS.

## Deployment

This CloudFormation template can be deployed using [AWS CLI][AWS CLI].
Make sure that the AWS CLI is [configured][AWS CLI Config] to deploy the stack to the target
AWS account.  Replace the values accordingly before executing the command.

```bash
    aws cloudformation package \
      --template-file cloudformation.yml \
      --output-template-file cloudformation.packaged.yml \
      --s3-bucket ${SOME_S3_BUCKET}

    aws cloudformation deploy \
      --stack-name RDS-Privatelink-Stack \
      --template-file cloudformation.packaged.yml \
      --capabilities CAPABILITY_NAMED_IAM \
      --parameter-overrides \
      VPCEndpointServiceAllowedPrincipals=arn:aws:iam::123456789:serviceconsumer \
      DnsServers=8.8.8.8 \
      RdsFQDN=mydatabase.cluster-ro-abcdefghijk.us-east-1.rds.amazonaws.com \
      SubnetList=subnet-12345678,subnet-23456789,subnet-34567890 \
      VpcId=vpc-12345678 \
      --no-execute-changeset
```

[AWS CLI]: https://aws.amazon.com/cli/
[AWS CLI Config]: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
[Hostname-as-Target for Network Load Balancers]: https://aws.amazon.com/blogs/networking-and-content-delivery/hostname-as-target-for-network-load-balancers/
