# AWS Production Architecture

A hands-on AWS project that builds a highly available web hosting foundation across two Availability Zones. The design places an Application Load Balancer in public subnets and EC2 web servers in private subnets managed by an Auto Scaling Group.

## Overview

This project demonstrates how core AWS networking and compute services work together to host a web server with controlled internet access and room to scale. The VPC uses the `10.0.0.0/16` IPv4 CIDR block and spans `ap-south-1a` and `ap-south-1b`.

## Architecture

```text
Users
  |
Route 53 DNS (if configured)
  |
Application Load Balancer in public subnets
  |
Target Group with health checks
  |
Auto Scaling Group
  |
EC2 Apache web servers in private subnets
  |                         \
NAT Gateway                  S3 Gateway VPC Endpoint
(outbound internet)          (private S3 access)
```

The report names Route 53 in the request path, but does not document its hosted zone or DNS record configuration. Confirm that DNS was configured before claiming that Route 53 was tested.

## AWS services and components

- **Amazon VPC** with IPv4 CIDR `10.0.0.0/16`
- **Two public and two private subnets** distributed across two Availability Zones
- **Internet Gateway** and public route table for public-subnet internet routing
- **NAT Gateway** with an Elastic IP for outbound access from private subnets
- **Security Groups** for instance traffic control
- **Amazon EC2** using a Launch Template and User Data to configure Apache
- **Application Load Balancer** and **Target Group** with health checks
- **Auto Scaling Group** to manage EC2 capacity
- **Amazon S3 Gateway VPC Endpoint** for private access from the VPC
- **Amazon S3** and **Amazon Route 53** are listed in the project report; add the bucket/DNS setup details if you configured them

## Request flow

A user request is intended to resolve through Route 53, reach the public Application Load Balancer, and be forwarded to a healthy EC2 instance registered in the target group. The Auto Scaling Group manages the EC2 instances. Private instances can use the NAT Gateway for outbound internet access and the S3 Gateway Endpoint for private S3 traffic.

## Implementation summary

1. Created a VPC with two public and two private subnets across `ap-south-1a` and `ap-south-1b`.
2. Associated public and private route tables with their subnets. The public route uses the Internet Gateway; private outbound access uses the NAT Gateway.
3. Created security groups and an EC2 Launch Template with an AMI, instance type, key pair, IAM role, security group, and User Data script.
4. Created a Target Group with health checks and an internet-facing Application Load Balancer in the public subnets.
5. Connected an Auto Scaling Group to the Launch Template and Target Group so it can manage the web-server instances.
6. Added an S3 Gateway VPC Endpoint for private access to Amazon S3.
7. Installed and enabled Apache on EC2. The report says the Apache default page was opened through an EC2 public IP.

## Validation

The project report lists these checks:

- EC2 instance running
- Application Load Balancer working
- Auto Scaling behavior
- Apache web page output

Add screenshots or command output for each check before presenting the items as verified results. The report describes opening Apache via an EC2 public IP; if the final instances are in private subnets, document the check through the Load Balancer DNS name instead, or explain the temporary public access setup used during testing.

## Security and cost notes

- Keep web instances in private subnets and expose the application through the Load Balancer.
- Restrict SSH access to a trusted IP range, or use AWS Systems Manager where configured. Avoid leaving SSH open to `0.0.0.0/0`.
- Use least-privilege IAM permissions for the EC2 role and S3 access.
- NAT Gateways, load balancers, EC2 instances, and public IPv4 addresses can incur charges. Remove resources when finished if the environment is no longer needed.

## Repository contents

Add your architecture diagram, sanitized screenshots, and any scripts or configuration files used to build the environment. Do not upload private keys, AWS credentials, account-specific secrets, or screenshots that expose sensitive information.

## Future improvements

- Add Infrastructure as Code with AWS CloudFormation, AWS CDK, or Terraform.
- Document the Auto Scaling policy and demonstrate scale-out and scale-in with evidence.
- Add Route 53 hosted zone and record details if DNS was configured.
- Document the S3 bucket policy and endpoint policy, if used.
- Add HTTPS listener and certificate details if TLS was configured.

## References

- [Amazon VPC documentation](https://docs.aws.amazon.com/vpc/)
- [Amazon EC2 documentation](https://docs.aws.amazon.com/ec2/)
- [Elastic Load Balancing documentation](https://docs.aws.amazon.com/elasticloadbalancing/)
- [Amazon EC2 Auto Scaling documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)
- [Amazon S3 documentation](https://docs.aws.amazon.com/s3/)
- [Amazon Route 53 documentation](https://docs.aws.amazon.com/route53/)
