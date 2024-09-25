+++
title = 'ISO 27001 Part 2'
date = 2024-09-22T03:33:35+02:00
draft = false
+++

# Annex A 8.2: Privileged Access Rights

This control is pretty simple to define and quite hard to implement. 

Services and people must have an access based on their needs. If your service requires an access to write to specific S3 bucket, do not allow access to whole S3 service. 

Administrator role should be granted only to specific group of people trained and prepared to perform those duties. Usually we allow infrastructure team to have an access everywhere though it's not necessary most of the times. In ideal scenario we would like to see full access only as an elevated privilege provided in case of incident and granted after another person approved it. In some cases additional approval might contribute to bigger downtime, instead of approval post-incident review of actions could be implemented as well.

## Implementation example (AWS)

Let's think about an example. AWS allows you to create an IAM user. Let's create also default "users" group and define a policy to allow only read-only access to cloud services. AWS also provide a way to assume specific roles and our AWS CLI config role could look something like that:

```
[profile default]
region = us-east-1
output = json

[profile production-rw]
role_arn = arn:aws:iam::ACCOUNT_ID:role/FullAccessRole
region = us-east-1
source_profile = default
```

In this case when user is logged in to AWS Console or run `aws` CLI, there is no way something could be changed. AWS profile should be specified to perform any operation that'll change something or, in case of AWS Console, user must use Switch Role in UI and be aware that actions could cause some damage. This approach also allows us to create a permission elevation system. For example, Lambda function could be triggered to allow user to assume `FullAccessRole` role for short amount of time and another Lambda function clean up all the privilege access after specific amount of time. This function also can go through the CloudTrail events, gather all the changes caused by this user and prepare a report.

Another great option is short TTL credentials. For example Hashicorp Vault allows to control service credentials like SSH keys or Database passwords, user has to specifically request for it and this access will be revoked shortly after. It even allows to create credentials for AWS itself, this method might be a great option in multicloud environment.

* [AssumeRole documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage-assume.html)
* [AWS IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
* [AWS IAM in EKS cluster](https://docs.aws.amazon.com/eks/latest/userguide/security-iam.html)
* [AWS credentials using Hashicorp Vault](https://developer.hashicorp.com/vault/docs/secrets/aws)

## Other clouds

GCP and Azure have their own implementation of the way to assume privileges. GCP calls it service account impersonation. Basically it allows you to pretend your user is a service account and act using their privileges. Azure has a concept called service principal. Its main idea to provide a way for service to authenticate. Human user can retrieve credentials for service principal and run CLI commands. Unfortunately I didn't find a nice way to raise privileges in UI for both Azure and GCP, the only way I found works is to assign additional temporary role.

When I started to work on GCP project for a FinTech company, I discovered hierarchy of projects matters a lot. For example, you can create an Organization, add all the IAM users who should have an access everywhere here, than create a Dev and Production folders. All the projects inside those folders will inherit users and privileges from organization. If you want to have users with an access only to development projects, you need to add them to Dev folder only. This way combination of folder+role will provide proper access to an user. The same way you can deal with service accounts.

* [Azure IAM best practices](https://learn.microsoft.com/en-us/azure/security/fundamentals/identity-management-best-practices)
* [GCP IAM best practices](https://cloud.google.com/blog/products/identity-security/iam-best-practice-guides-available-now)
* [GCP credentials using Hashicorp Vault](https://developer.hashicorp.com/vault/docs/secrets/gcp)
* [Azure credentials using Hashicorp Vault](https://developer.hashicorp.com/vault/docs/secrets/azure)


## What could go wrong, right?

Key component here is to know who needs access and where, what's the minimal amount of privileges will allow to perform duties. Clouds have enormous amount of different privileges you could grant so preparation of those roles, groups and policies must be done thoroughly first, than you could think about safeguards and other improvements.