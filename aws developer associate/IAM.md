# Identity and Access Management

Manage users and their level of access to the AWS console

## What it does
- Centralized: control of your aws account
- Access: Shared access of your aws account
- Permission: Granular permission
- Identity federation: supports well-known identity providers such as Active Directory, Facebook, and Linkedin
- Provides multi factor authentication
- Provide temporary access for users/devices and services
- Password policies rotation
- Integrated with many aws account
- Support PCI compliance

## User, groups, roles
- User: end user
- Group: a collection of user under one set of permission
- Roles: create roles then assign them to users, applications, services
- Policy: document that defines one or more permissions => can be attach to a user, a group or role

##  Testing IAM policy permissions with IAM policy simulator
- Test the effects of IAM before committing them to production
- Validate that the policy works
- Test policies already attached to existing users

## Exam tips
- IAM is universal, it doesnt apply to regiion at this time
- New users have no permission when created
- Access keys: new users are assigned access key id and secret access key when their accounts are created
=> You only get to view the access key id and secret key once. If you lose them, you have to regenerate them.
=> Use MFA
=> Customize Password rotation

## Example question
1. Which is the best way to enable S3 read-access for an EC2 instance?

Create an IAM role with read-access to S3 and assign the role to the EC2 instance
=> Correct. IAM roles allow applications to securely make API requests from instances, without requiring you to manage the security credentials that the applications use.

2. Which IAM entity can delegate AWS resource access for users, groups, or services?

IAM Role