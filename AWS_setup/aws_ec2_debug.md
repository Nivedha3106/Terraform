# AWS EC2 Troubleshooting Log (Terraform)

This document contains a log of issues I faced while provisioning an EC2 instance using Terraform, and how I resolved them — step by step.

---

## Error 1: UnauthorizedOperation (403)

**Error Message:**
creating EC2 Instance: operation error EC2: RunInstances, https response error StatusCode: 403, RequestID: f07b9c40-27a5-4afb-95d1-29db5953cde0, api error UnauthorizedOperation: You are not authorized to perform this operation.

### Root Cause:
Although I had the `AmazonEC2FullAccess` policy, my **IAM user had a permissions boundary** that did not allow `ec2:RunInstances`.

### Resolution Steps:

1. **Check if a permissions boundary is attached** to the IAM user.
   - Go to **AWS Console > IAM > Users > [Your User] > Permissions**.
2. **Update the permissions boundary policy** to include the required action:

```json
{
  "Effect": "Allow",
  "Action": "ec2:RunInstances",
  "Resource": "*"
}

## Error 2: Invalid AMI ID

**Error Message:**
Error: creating EC2 Instance: operation error EC2: RunInstances, 
api error InvalidAMIID.NotFound: The image id '[ami-0c55b159cbfafe1f0]' does not exist

### Root Cause:
The AMI ID provided in the Terraform configuration was either:

1. Wrong/inactive
2. Not available in the region I was deploying to

### Resolution Steps:
- Go to the AWS Console → EC2 > AMIs → search for a valid AMI in your region (e.g., ap-south-1).
- Copy the new AMI ID (e.g., for Amazon Linux 2).
- Update your Terraform code
