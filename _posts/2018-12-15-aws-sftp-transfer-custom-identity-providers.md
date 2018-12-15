---
layout: post
title: "AWS SFTP Transfer with a Custom Identity Provider"
date: 2018-12-15
categories:
  - aws
---

We were excited when AWS _finally_ [announced](https://aws.amazon.com/blogs/aws/new-aws-transfer-for-sftp-fully-managed-sftp-service-for-amazon-s3/) a hosted SFTP transfer service backed directly by S3. We had looked at other solutions that mounted S3 as a drive, but they always seem to have some random issues. What we had settled on was running an ftp server and then periodically sweeping the uploaded files into S3 for processing.

The announcement made the setup process look simple, which it was for the specific use case where all users will manage their own ssh certificates. In our case though, users had to be able to upload files with just a username and password. And, ideally, it would be the same username and password they use to work in our other systems.

According to the [documentation](https://docs.aws.amazon.com/transfer/latest/userguide/authentication-custom-ip.html) for Custom Identity Providers, it should be a simple process of setting up a lambda and API Gateway end point. On a successful authentication, the lambda should return this json back to the SFTP Transfer service.

```json
{
  "Role": "IAM role with configured S3 permissions",
  "PublicKeys": ["ssh-rsa public-key1", "ssh-rsa public-key2"],
  "Policy": "STS Assume role scope down policy",
  "HomeDirectory": "User's home directory"
}
```

- We were not using PublicKeys, so they can be omitted.
- The users home directory really means the full path, including the bucket name. For example, `/sftp-bucket/username`

Role and policy were not so simple. First, read this [post](https://www.yobyot.com/aws/how-aws-transfer-for-sftp-works-with-iam-route53-cloudwatch/2018/12/03/) about IAM Assume role, and how to setup the basic scope-down [policy][1]. The writer does a good job explaining both, so there is no need to rehash the same thing here. But, that post only deals with setting up users that are managed by the SFTP Transfer service and not dealing with a custom identity provider.

Looking back as the require json response, anyone who has experience with AWS IAM would look at the json above and think `Role` and `Policy` require ARNs. It turns out we were only half right. Yes, `Role` does require an ARN. The role must also have full access to the ftp bucket. The policy is given a set of variables by the SFTP Transfer service, and is applied in real time. In order for this to work, the policy **must** be passed inline. Let me repeat that, the entire policy must be returned inline from the lambda function.

We could not find this gotcha in the documentation. There were hints in how scope-down policies are used though. Because they are assumed at usage time and passed variables, they cannot be attached to roles ahead of time. We incorrectly thought that the SFTP Transfer service would look up the policy based on its ARN, and do the replacement then. Instead, it treated the ARN as json which obviously failed and gave the final clue to the issue - a CloudWatch error from the SFTP Transfer service that read _invalid json_ when a user logged in.

Finally, since we like to manage all our IAM policies through an external tool like Terraform or CloudFormation we did not want a random policy sitting in a lambda function. We went ahead and implemented what we thought the SFTP Transfer service was doing and looked up the policy json using the AWS SDK `getPolicy()` and `getPolicyVersion()` functions. This way the policy is managed along with all our other policies, and is dynamically loaded in the lambda with the json returned.

[1]: https://forums.aws.amazon.com/thread.jspa?messageID=881404#881404 "A stricter scope-down policy example can be found in the comments."
