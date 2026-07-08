---
title: "Deploy and Test Backend"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Deploying with AWS SAM

```bash
sam build
sam deploy --resolve-s3
```

## Issues encountered during deployment

| # | Issue | Cause | Fix |
|---|---|---|---|
| 1 | `S3 Bucket not specified` | First deploy without `--guided`, no managed bucket yet | Use the `--resolve-s3` flag so SAM creates a managed bucket automatically |
| 2 | Stack `aws-sam-cli-managed-default` stuck in `ROLLBACK_COMPLETE` | IAM user `inboxiq-dev` was missing `s3:CreateBucket` and `cloudformation:*` permissions | Attached `AmazonS3FullAccess` and `AWSCloudFormationFullAccess`, deleted the failed stack, redeployed |
| 3 | `USER_PASSWORD_AUTH flow not enabled` when fetching a test JWT | The App Client only had `ALLOW_USER_SRP_AUTH` enabled by default | Enabled `ALLOW_USER_PASSWORD_AUTH` via `update-user-pool-client` (keeping the existing flows) |
| 4 | Worker Lambda `SyntaxError` parsing the OpenAI key | Creating the secret via PowerShell dropped the double quotes in the JSON | Wrote the JSON to a file (`-Encoding ascii`), pointed `--secret-string file://...` at it |

## Deployment result

```
Outputs
-----------------------------------------------
RestApiEndpoint: https://rjc76njhya.execute-api.us-east-1.amazonaws.com/prod
WebSocketEndpoint: wss://xmz4gyqica.execute-api.us-east-1.amazonaws.com/prod

Successfully created/updated stack - inboxiq-backend in us-east-1
```

![Deployment succeeded](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/deploy-success-outputs.jpg)

## CloudWatch Alarms

3 monitoring alarms: Worker error rate, messages landing in the DLQ, and estimated cost exceeding $5.

![CloudWatch Alarms](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/cloudwatch-alarms.jpg)

## End-to-end test

Created a test user via `aws cognito-idp admin-create-user`, obtained a JWT token via `initiate-auth`, and called the REST API with `curl.exe` using that token.

**Result:** received `202 Accepted`, the message landed in SQS, the Worker Lambda was triggered automatically, and processing went through: fetch OpenAI key (Secrets Manager) → query Gmail token (DynamoDB) → **stopped exactly where expected**, since the test user has no Gmail OAuth token yet:

```
Worker received 1 messages
Processing jobId=job-1783439894695-xlcyzo for userId=d4480488-...
ERROR: No Gmail token for user d4480488-c031-7014-0d67-6eed60f23063
    at getGmailToken (file:///var/task/index.mjs:43:27)
```

![Worker log stopping at the expected point](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/worker-log-no-gmail-token.jpg)

> This error is **not a bug** — it confirms the entire Producer → SQS → Worker → Secrets Manager → DynamoDB flow works as designed, stopping exactly at the one missing piece (the Gmail OAuth token), which will be completed in the next section — **5.4 Gmail OAuth Setup**.
