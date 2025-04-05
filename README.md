# 🌐 Static Website Hosting on AWS (S3 + CloudFront + Route53 + ACM)

This guide helps you set up a secure, fast, and scalable static website using:

- ✅ Amazon S3 (for storage)
- ✅ CloudFront (for CDN and caching)
- ✅ Route 53 (for domain routing)
- ✅ AWS Certificate Manager (for HTTPS)

---

## 🚀 Prerequisites

- An AWS account
- A registered domain (e.g., from Route 53 or elsewhere)
- AWS CLI configured (optional but useful)

---

## 🪣 Step 1: Create a Private S3 Bucket

1. Go to [S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Click **Create bucket**.
3. Choose a **unique name** (e.g., `my-website-bucket`).
4. Keep **Block all public access** ✅ enabled.
5. Click **Create bucket**.

### 🔼 Upload your website files

- Upload `index.html`, `404.html`, `style.css`, `script.js`, etc.

---

## 🌍 Step 2: Set Up CloudFront Distribution

1. Go to [CloudFront Console](https://console.aws.amazon.com/cloudfront/v3/home).
2. Click **Create Distribution**.
3. Under **Origin**:
   - Origin domain: Select your S3 bucket.
   - Origin access: Choose **Origin access control (recommended)**.
   - Click **Create control setting**, name it (e.g., `S3-OAC`), and create.
4. Set **Viewer Protocol Policy** to `Redirect HTTP to HTTPS`.
5. Set **Default root object** to `index.html`.
6. Click **Create Distribution**.

---

## 🔒 Step 3: Attach OAC Permissions to S3 Bucket

1. Go to your **S3 bucket** → **Permissions** → **Bucket Policy**.
2. Add the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<BUCKET_NAME>/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::<ACCOUNT_ID>:distribution/<DISTRIBUTION_ID>"
        }
      }
    }
  ]
}
