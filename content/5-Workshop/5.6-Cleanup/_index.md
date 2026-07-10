---
title: "Cleanup Resources"
date: 2026-07-09
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

After the demo and report submission are complete, all AWS resources provisioning InboxIQ should be cleaned up to avoid unexpected charges. This section walks through the cleanup steps in the correct order, avoiding accidental deletion of a resource still depended on by another.

#### 5.6.1 Delete the main CloudFormation stack (Lambda, API Gateway, SQS, DynamoDB)

The entire backend is managed by a single SAM stack, so it can be removed with one command:

```powershell
cd D:\AWS\Project\AWSF\inboxiq-backend
sam delete
```

This prompts for confirmation before deleting anything — verify the stack name is `inboxiq-backend` before confirming:

```
Are you sure you want to delete the stack inboxiq-backend in the region us-east-1 ? [y/N]:
Are you sure you want to delete the folder inboxiq-backend in S3 which contains the artifacts? [y/N]:
```

`sam delete` automatically removes every resource declared in `template.yaml`: 8 Lambda functions, the REST API, the WebSocket API, 4 DynamoDB tables, the SQS queue (including its dead-letter queue), the Lambda Layer (all versions), and the related Lambda permissions.


![Terminal showing sam delete confirmation](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-sam-delete-confirm.jpg)

#### 5.6.2 Verify in the CloudFormation Console after deletion

Go to the **CloudFormation Console** → find the `inboxiq-backend` stack → confirm its status has changed to `DELETE_COMPLETE` (the stack disappears from the default list once deleted; enable "Show nested" or filter by status to still see it).


![CloudFormation stack inboxiq-backend before deletion](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cfn-stack-before.jpg)

#### 5.6.3 Delete the Cognito User Pool

The User Pool was created manually in an earlier step (file 02) and is not part of the SAM stack, so it needs to be deleted separately:

1. Go to the **Cognito Console** → **User pools** → select the InboxIQ user pool.
2. **Delete** (top-right of the pool overview page) → type the pool name to confirm → **Delete**.

![Cognito User Pool before deletion](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cognito-pool.jpg)

#### 5.6.4 Delete the Secrets Manager secrets

InboxIQ stores two pieces of sensitive information in Secrets Manager: the OpenAI API key and the Google OAuth Client Secret. Both need to be deleted:

```powershell
aws secretsmanager delete-secret `
  --secret-id inboxiq/openai-api-key `
  --region us-east-1

aws secretsmanager delete-secret `
  --secret-id inboxiq/google-oauth-client `
  --region us-east-1
```

{{% notice tip %}}
By default, Secrets Manager keeps a secret in a "pending deletion" state for 7-30 days before permanently removing it (so an accidental deletion can still be recovered). To delete it immediately, add the `--force-delete-without-recovery` flag to each command — consider this carefully, as the action cannot be undone.
{{% /notice %}}

![Secrets Manager - secrets before deletion](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-secrets-manager.jpg)

#### 5.6.5 Delete the S3 bucket holding SAM deployment artifacts

`sam delete` in step 5.6.1 usually asks whether to also delete the artifact bucket (`aws-sam-cli-managed-default-samclisourcebucket-...`). If that was skipped, delete it manually:

1. Go to the **S3 Console** → find the bucket with the prefix `aws-sam-cli-managed-default-samclisourcebucket-`.
2. **Empty** the bucket first (S3 won't allow deleting a bucket that still contains objects) → then **Delete** the bucket.

![List of S3 SAM artifact buckets before deletion](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-s3-bucket.jpg)

#### 5.6.6 Check remaining CloudWatch Logs

Log groups are usually deleted automatically along with their Lambda function via `sam delete`. If any remain (created manually, or retention hasn't expired yet), filter by prefix to clean them up:

1. Go to the **CloudWatch Console** → **Log groups** → filter by `/aws/lambda/inboxiq-`.
2. Select all → **Actions** → **Delete log group(s)**.

![List of remaining CloudWatch Log groups](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cloudwatch-logs.jpg)

#### 5.6.7 Resource cleanup summary

| # | Resource | How to delete | Section |
|---|---|---|---|
| 1 | Lambda, API Gateway, DynamoDB, SQS, Layer | `sam delete` | 5.6.1 |
| 2 | Cognito User Pool | Manual deletion via Console | 5.6.3 |
| 3 | OpenAI API key secret | `aws secretsmanager delete-secret` | 5.6.4 |
| 4 | Google OAuth Client secret | `aws secretsmanager delete-secret` | 5.6.4 |
| 5 | S3 SAM artifact bucket | Empty + Delete via Console | 5.6.5 |
| 6 | Remaining CloudWatch Log groups | Manual deletion if needed | 5.6.6 |
