# Identity and Resource Policies

* [IAM Policy Elements](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)
* [IAM Policy Elements: Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)
* [IAM Policy Elements: Variables and Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html)

* **Exam Tips:**
  * Policy General:
    * Expect a question on policy variables.
  * Principal:
    * A principal is not required (or [allowed](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)) in an identity policy - It is assumed.
    * It is required in a resource policy.
      * Easy way to quickly distinguish between a **resource** policy and an **identity** policy is the *__presence of the principal statement__*.
    * It is allowed in trust policies for IAM roles.
    * Principals can be:
      * AWS account and root
      * IAM users
      * Federated users (web identity or SAML)
      * IAM roles
      * Assumed-role sessions
      * AWS services
      * Anonymous users
    * Cannot use a wildcard (*) when users are specified as the principal. Principals must be specifically named.
  * Version:
    * Always use the version statement: "_2012-10-17_"
      * Failure to use "_2012-10-17_" means it will default to the older version of the policy language "_2008-10-17_".
      * "_2008-10-17_" does not support items such as variables.
  * Required Elements:
    * Statement
    * Effect

## Policy Document Examples

### General S3 Allow Policy

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```

### Specific S3 Policy

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action":[
                "s3:ListAllMyBuckets"
            ],
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource":"arn:aws:s3:::bucket-name"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::bucket-name/*"
        }
    ]

}
```

### Specific Folder in an S3 Bucket Policy: Without Variables

```JSON
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": ["s3:ListBucket"],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::bucketname"],
      "Condition": {"StringLike": {"s3:prefix": ["username/*"]}}
    },
    {
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::bucketname/username/*"]
    }
  ]
}
```

### Specific Folder in an S3 Bucket Policy: With Variables

```JSON
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": ["s3:ListBucket"],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::bucketname"],
      "Condition": {"StringLike": {"s3:prefix": ["${aws:username}/*"]}}
    },
    {
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::bucketname/${aws:username}/*"]
    }
  ]
}
```