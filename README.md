# 🔐 AWS Lambda GRC Automation – NIST AC-2(3) Compliance

> Automates NIST 800-53 AC-2(3) – Disable Inactive Accounts using AWS Lambda. Detects and optionally disables IAM users inactive for 90+ days. Built for GRC automation, audit evidence collection, and continuous compliance in AWS environments.

![badge](https://img.shields.io/badge/AWS-Lambda-orange) ![badge](https://img.shields.io/badge/Compliance-NIST%20800--53-blue)

This project automates the enforcement of **NIST 800-53 AC-2(3): Disable Inactive Accounts** using AWS Lambda. It identifies IAM users who haven’t logged in for 90+ days and either logs or disables them. This aligns with modern **continuous compliance** and **GRC automation** goals for cloud-native organizations.

## 🎯 Use Case: Continuous GRC Automation

Organizations using AWS must ensure IAM accounts are disabled after a defined period of inactivity. This project achieves that by:

- 🔍 Detecting stale IAM accounts  
- 🔐 Optionally disabling login access  
- 🧾 Generating audit-ready logs  
- 📤 Sending evidence to CloudWatch or S3  

This makes it ideal for:
- Cloud Security Engineers  
- GRC & Compliance Teams  
- DevSecOps Workflows

## 🧱 Architecture Overview

```
+------------------+         +-----------------+
| AWS IAM Users    | <--->   | AWS Lambda      |
+------------------+         +-----------------+
                                  |
                                  v
                          +-------------------+
                          | CloudWatch Logs   |
                          +-------------------+
                                  |
                                  v
                          +-------------------+
                          | (Optional) S3      |
                          | Audit Bucket       |
                          +-------------------+
```

*You may also include this as `architecture/grc-lambda-architecture-diagram.png` in your repo.*

## 🚀 How to Deploy

### 1. Upload Lambda Code to S3

Upload the ZIP file to your S3 bucket:

```
s3://grc-lambda-deployments/lambda/control-ac2-inactive-accounts.zip
```

### 2. Create IAM Execution Role

Create a role named `LambdaGRCExecutionRole` and attach:

- **Managed policy**: `AWSLambdaBasicExecutionRole`  
- **Inline policy**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:ListUsers",
        "iam:GetUser",
        "iam:GetLoginProfile",
        "iam:UpdateLoginProfile",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

### 3. Create the Lambda Function

- Go to AWS Lambda → Create function  
- Runtime: `Python 3.9`  
- Handler: `lambda_function.lambda_handler`  
- Upload code from:  
  - S3 Bucket: `grc-lambda-deployments`  
  - Key: `lambda/control-ac2-inactive-accounts.zip`  
- Assign the role: `LambdaGRCExecutionRole`

### 4. Test It

1. Go to the Lambda function → Test tab  
2. Create a new test event (can be `{}`)  
3. Click **Test**  
4. View execution results in the console  
5. Navigate to **CloudWatch Logs** to verify:

```
Disabling user: dev-admin
Disabling user: finance-support
```

## 🔍 How It Works

1. Lists all IAM users  
2. Evaluates each user’s `PasswordLastUsed` timestamp  
3. If a user hasn’t logged in for 90+ days:
   - Logs the result to **CloudWatch Logs**
   - Optionally disables login by resetting the login profile

## 📁 Folder Structure

```
aws-lambda-grc-ac2-inactive-iam-users/
├── lambda/
│   └── control-ac2_inactive_accounts/
│       └── lambda_function.py
├── terraform/
├── evidence/
├── architecture/
│   └── grc-lambda-architecture-diagram.png
├── screenshots/
│   ├── lambda-overview.png
│   ├── test-output.png
│   ├── cloudwatch-logs.png
│   ├── iam-role-policy.png
│   └── s3-upload.png
├── README.md
```

## 📸 Screenshots

| View                | Screenshot                  |
|---------------------|-----------------------------|
| Lambda Function     | ![](screenshots/lambda-overview.png) |
| Test Output         | ![](screenshots/test-output.png)     |
| CloudWatch Logs     | ![](screenshots/cloudwatch-logs.png) |
| IAM Role Policy     | ![](screenshots/iam-role-policy.png) |
| S3 ZIP Upload       | ![](screenshots/s3-upload.png)       |

## 📁 Evidence Collection

- **CloudWatch Logs** show the time, user affected, and action  
- You can modify the Lambda to write JSON to:  
  - `s3://your-evidence-bucket/ac2-logs/`  
  - DynamoDB for audit trails

## ✅ Compliance Mapping

| Framework     | Control ID | Description                          |
|---------------|------------|--------------------------------------|
| NIST 800-53   | AC-2(3)    | Disable Inactive Accounts            |
| CIS Benchmark | 1.20       | Review IAM user activity             |

## 🧠 Learning Objectives

- ✅ Automate security controls using AWS Lambda  
- ✅ Build Compliance-as-Code infrastructure  
- ✅ Collect and organize audit evidence  
- ✅ Deploy and monitor serverless GRC tools  
- ✅ Align cloud infrastructure to NIST/CIS frameworks  
