# Introduction

AWS Identity and Access Management enables fine grained access control to AWS resources. AWS IAM is a completely free to use service and a fundamental service to have security for everything in AWS.

- `AWS services` need to be managed
- This requires access to these services
- These services allow us to work with `AWS resources`
- IAM allows us to **manage access securely**

## What are we going to learn

This chapter covers misconfigurations in AWS IAM policies & how that can be used for malicious purpose.

## What are the misconfigurations

AWS IAM policy mis-configurations can occur in various ways. This could be due to a broad Action or broad Resource or a combination of both, resulting in overly permissive policies. Alternatively, since AWS allows you to have upto 5 versions of a policy, only one of which is active at any given moment, there could be a policy that has a vulnerable older version that can be activated instead.

`aws iam set-default-policy-version –policy-arn target_policy_arn –version-id v2`

The first example that we saw with common mis-configurations was a policy that was not bound to a particular Action or Resource.

- usage of wildcard* not bound to action/resource. Here we have a policy that when evaluated by AWS, effectively gives the user or the role Administrative Access to the AWS account. This is one of the most dangerous policy definitions and must never be used unless absolutely required within AWS.

```json
{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": "*",
                "Effect": "Allow",
                "Resource": "*"
            }
        ]
}
```

- Using "Action" that could lead to impersonation of another user.

```json
{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": "sts:AssumeRole",
                "Effect": "Allow",
                "Resource": "*"
            }
        ]
}
```

- Here we have is that of the presence of sts:AssumeRole. This operation allows the user or the role to assume the privileges of a role in AWS.

- For example, if we had access to a user with the sts:AssumeRole operation allowed, and a role with an ARN of arn:aws:iam::123456789123:role/adminrole exists on AWS for the account then the following command would generate an AWS_ACCESS_KEY_ID, a AWS_SECRET_ACCESS_KEY, and a AWS_SESSION_TOKEN which could then be used to impersonate the role with all of its privileges.

```aws sts assume-role --role-arn arn:aws:iam::123456789123:role/adminrole --role-session-name impersonatingrole```


