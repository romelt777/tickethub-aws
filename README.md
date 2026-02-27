# TicketHub - Serverless Ticketing System

TicketHub is a serverless ticket submission app I built to get hands-on with AWS. The frontend is Next.js, the backend uses AWS services, with Lambda handling the core application logic. The infrastructure is defined using AWS SAM, so everything can be recreated and deployed from code..

[![Live Demo](https://img.shields.io/badge/demo-live-success)](https://tickethub-aws-ui.vercel.app/)
[![AWS](https://img.shields.io/badge/AWS-Lambda%20%7C%20SQS%20%7C%20DynamoDB%20%7C%20X--Ray-orange)](https://aws.amazon.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-black)](https://nextjs.org)
[![SAM](https://img.shields.io/badge/IaC-AWS%20SAM-yellow)](https://aws.amazon.com/serverless/sam/)

---

**Live Demo:** [https://tickethub-aws-ui.vercel.app/](https://tickethub-aws-ui.vercel.app/)

---

## How it works

When a user submits a ticket, the request goes through API Gateway which triggers a validation Lambda. 

The Lambda checks the input and returns a `400` response if validation fails.  
If the input is valid, the ticket is sent to an SQS queue.

A separate Lambda processes messages from the queue and writes the ticket to DynamoDB.

If processing fails, SQS automatically retries the message up to three times. After that, the message is moved to a Dead-Letter Queue so it can be inspected and replayed.


---

## Architecture

**Frontend → API Gateway → Validator Lambda → SQS Queue → Processor Lambda → DynamoDB**

If a message fails processing after three retries, it is moved to a Dead-Letter Queue.

---

## Tech Stack

**Frontend**
- Next.js 15, React 19, TailwindCSS
- Deployed on Vercel

**Backend**
- API Gateway
- AWS Lambda (Node.js)
- Amazon SQS with Dead-Letter Queue
- Amazon DynamoDB
- AWS X-Ray for tracing

**DevOps**
- AWS SAM (infrastructure as code)
- AWS SDK v3

---

## Infrastructure as Code

All backend resources are defined in a single SAM `template.yaml` file, including:

- Lambda functions  
- API Gateway  
- SQS and Dead-Letter Queue  
- DynamoDB  
- IAM permissions  

## Running Locally

### 1. Clone the repository

```bash
git clone https://github.com/romelt777/tickethub-backend.git
cd tickethub-backend
```

### 2. Build and deploy with SAM
```bash
sam build
sam deploy --guided
```


## Tracing

AWS X-Ray tracing is enabled across the full request flow.

This lets me see how a request moves through API Gateway, both Lambda functions, the SQS queue, and finally DynamoDB. It’s helpful for understanding latency between services and debugging failures.

![XRay trace map](./docs/xray-trace-diagram-screenshot.png)

---

## Repositories

- **Frontend:** https://github.com/romelt777/tickethub-aws-ui  
- **Backend:** https://github.com/romelt777/tickethub-backend  

---

## What's next

Things I’d like to add or improve:

- CloudWatch alarms for error rates and queue depth  
- Email confirmations using SES  
- Authentication with Cognito  
- A simple admin dashboard for managing tickets  
