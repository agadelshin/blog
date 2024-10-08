+++
title = 'EKS CSI Driver deployment (terraform)'
date = 2024-09-30T19:07:31+02:00
draft = false
+++

For some non-obvious reason it wasn't straight-forward how to deploy/configure AWS EBS CSI controller even after reading the docs. Key thing here is you have to install csi driver yourself and the easiest way to do it is to use EKS addons. You can also tweak eks addons deployment using `configuration_values` property to change resources, add nodeSelector, etc.

Here is the terraform configuration for that with some ommited fields not related to the configuration of storage

```hcl
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 19.0"

  cluster_addons = {
    aws-ebs-csi-driver = {
      most_recent              = true
      service_account_role_arn = aws_iam_role.aws_csi.arn
    }
  }

  enable_irsa = true
}

resource "aws_iam_role" "aws_csi" {
  name = "eks-aws-csi"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRoleWithWebIdentity"
        Effect = "Allow"
        Sid    = ""
        Principal = {
          Federated = "${module.eks.oidc_provider_arn}"
        }
        Condition = {
          StringEquals = {
            "${module.eks.oidc_provider}:sub" = "system:serviceaccount:kube-system:ebs-csi-controller-sa",
            "${module.eks.oidc_provider}:aud" = "sts.amazonaws.com",
          }
        }
      },
    ]
  })
}

resource "aws_iam_role_policy_attachment" "aws_csi" {
  role       = aws_iam_role.aws_csi.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy"
}

```

It'll probably also have some conflict during the first installation, because eks module needs role and role needs oidc information from eks.
