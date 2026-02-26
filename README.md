# TicketHub - Serverless Ticketing System

TicketHub is a serverless ticket submission app I built to get hands-on with AWS. The frontend is Next.js, the backend runs entirely on AWS Lambda, and the whole infrastructure is defined as code using AWS SAM so it can be deployed from scratch with a single command.

[![Live Demo](https://img.shields.io/badge/demo-live-success)](https://tickethub-aws-ui.vercel.app/)
[![AWS](https://img.shields.io/badge/AWS-Lambda%20%7C%20SQS%20%7C%20DynamoDB%20%7C%20X--Ray-orange)](https://aws.amazon.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-black)](https://nextjs.org)
[![SAM](https://img.shields.io/badge/IaC-AWS%20SAM-yellow)](https://aws.amazon.com/serverless/sam/)

---

**Live Demo:** [https://tickethub-aws-ui.vercel.app/](https://tickethub-aws-ui.vercel.app/)

---

## How it works

When a user submits a ticket, the request hits API Gateway which triggers a validation Lambda. If the input is invalid it returns a 400 with error details. If it passes, the ticket gets sent to an SQS queue. A second Lambda picks it up from the queue and writes it to DynamoDB.

If the processor fails, SQS retries the message up to 3 times. After that it gets moved to a dead-letter queue instead of disappearing silently, so nothing gets lost and failed messages can be inspected and replayed.

---

## Architecture

**Frontend → API Gateway → Validator Lambda → SQS Queue → Processor Lambda → DynamoDB**

On SQS failure after 3 retries → **Dead-Letter Queue**

---

## Stack

**Frontend**
- Next.js 15, React 19, TailwindCSS
- Deployed on Vercel

**Backend**
- API Gateway
- AWS Lambda (Node.js)
- Amazon SQS + Dead-Letter Queue
- Amazon DynamoDB
- AWS X-Ray for distributed tracing

**DevOps**
- AWS SAM (infrastructure as code)
- GitHub Actions CI/CD
- AWS SDK v3

---

## Infrastructure as Code

Everything is defined in a SAM `template.yaml`: Lambda functions, API Gateway, SQS, DLQ, DynamoDB, and IAM permissions. 

```bash
sam build
sam deploy --guided
```

## Tracing

X-Ray tracing is enabled across the full request path. You can see exactly how long each step takes: from the API Gateway through both Lambdas, the SQS handoff, and the DynamoDB write. Useful for spotting where things slow down or break.

Trace Map Diagram from AWS:
![XRay Diagram](./docs/xray-trace-diagram-screenshot.png)

---

## Repos

| Component | Repo |
|-----------|------|
| Frontend | [tickethub-aws-ui](https://github.com/romelt777/tickethub-aws-ui) |
| Backend (SAM) | [tickethub-backend](https://github.com/romelt777/tickethub-backend) |

---

## What's next

- CloudWatch alarms for error rates and queue depth
- Email confirmations with SES
- Auth with Cognito
- Admin dashboard for managing tickets
