# S3 Static Website Hosting - Commands

## S3 Bucket Policy

The following bucket policy was used to allow public read access to objects in the S3 bucket.

Replace `YOUR-BUCKET-NAME` with the actual S3 bucket name.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

## Policy Breakdown

| Field       | Purpose                                            |
| ----------- | -------------------------------------------------- |
| `Version`   | Specifies the AWS policy language version          |
| `Effect`    | Allows the specified action                        |
| `Principal` | Allows access from everyone (`*`)                  |
| `Action`    | Allows objects to be retrieved with `s3:GetObject` |
| `Resource`  | Applies the policy to objects inside the S3 bucket |

## Notes

The S3 bucket, static website hosting, public access settings, file uploads, and website configuration were completed through the **AWS Management Console**.

No AWS CLI commands were required for this deployment.
