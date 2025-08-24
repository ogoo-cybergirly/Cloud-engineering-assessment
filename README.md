# Cloud-engineering-assessment
This is my AltSchool third-semester Assessment on storing files in an S3 bucket and creating and configuring a VPC.

CloudLaunch Infrastructure Project
Task 1: S3 Buckets + IAM
- Created three S3 buckets:
  - cloudlaunch.site.bucket → Public static website hosting.  
  - cloudlaunch.private.bucket → Restricted to IAM user with `GetObject`/`PutObject` only.  
  - cloudlaunch.visible.only.bucket → IAM user can list but not read contents.  

- Uploaded the HTML file (`THEE Enterprise.html`) to cloudlaunch.site.bucket.  

- Configured bucket policies and IAM permissions.

Static Site Link
S3 Website Link: [http://cloudlaunch.site.bucket.s3-website-eu-west-1.amazonaws.com]

CloudFront was set up

IAM Policy JSON
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::cloudlaunch.site.bucket",
                "arn:aws:s3:::cloudlaunch.private.bucket",
                "arn:aws:s3:::cloudlaunch.visible.only.bucket"
            ]
        },
        {
            "Effect": "Allow",
            "Action": ["s3:GetObject", "s3:PutObject"],
            "Resource": "arn:aws:s3:::cloudlaunch.private.bucket/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch.site.bucket/*"
        }
    ]
}

```
Created a VPC cloudlaunch-vpc (10.0.0.0/16).
Subnets created include:
Public Subnet: 10.0.1.0/24 (for LB or future public services).
App Subnet: 10.0.2.0/24 (private).
DB Subnet: 10.0.3.0/28 (private).

Attached IGW cloudlaunch-igw.

Created route tables:
cloudlaunch-public-rt → Associated with public subnet, IGW route.
cloudlaunch-app-rt → Associated with app subnet, no internet access.
cloudlaunch-db-rt → Associated with db subnet, no internet access.

Security Groups:
cloudlaunch-app-sg → HTTP (80) allowed inside VPC.
cloudlaunch-db-sg → MySQL (3306) allowed from app subnet only.

IAM User Access
IAM User: cloudlaunch-uploader
This user has access is limited to the above three buckets, no delete permissions, and read-only access to VPC and networking components.

AWS Console Login

Console URL / Account Alias: https://419173572656.signin.aws.amazon.com/console
Username: 'cloudlaunch-uploader'
Password: attached to the Google Drive document

