# Blog Generation System

## 1. Purpose of the Project

This project aims to develop an end-to-end Generative AI application for blog generation.

### Key Goals:
- Generate blog content (200–300 words) based on a user-provided topic.
- Integrate various AWS services for a seamless GenAI workflow.
- Provide an API endpoint for invoking blog generation.
- Use Amazon Bedrock for accessing Foundation Models (FMs).
- Save generated content in AWS S3 as a text file or PDF.

---

## 2. Implementation Steps (Step-by-Step Guide)

This solution uses API Gateway, Lambda, Bedrock, and S3 for blog generation.

---

### Step 1: AWS Account and Bedrock Model Access
- Ensure your AWS account has access to Foundation Models via Amazon Bedrock.
- Supported models include Titan, Llama 2, Jurassic 2, Command, Claude 3, etc.
- Manage model access in the Bedrock console (e.g., for Llama 2).

---

### Step 2: Create AWS Lambda Function
- Use Python 3.12 as runtime and increase timeout (~3 minutes).
- Lambda function name: `AWS_app_with_bedrock`.

---

### Step 3: Develop Core Python Code
- Use a virtual environment and key libraries: `boto3`, `botocore.config`, `json`.
- **blog_generate_using_Bedrock(blog_topic)**:
  - Constructs LLM prompt (e.g., "[INST] Write a 200-word blog on...").
  - Prepares the request body with `max_gen_length`, `temperature`, etc.
  - Uses `bedrock.invoke_model()` with appropriate `modelId` (e.g., `meta.llama2-13b-chat-v1`).
  - Parses the response and returns the generated content.
- **lambda_handler(event, context)**:
  - Parses `event["body"]["blog_topic"]`.
  - Calls blog generator and saves result to S3.
  - Returns JSON response with status.

---

### Step 4: Deploy Lambda Code and Layers
- Add Python code in Lambda editor and deploy.
- **Create Lambda Layer for boto3**:
  ```bash
  mkdir python && pip install boto3 -t python/
  zip -r boto3_layer.zip python/
  ```
- Upload as a Lambda Layer and attach to your function.

---

### Step 5: Setup API Gateway
- Create HTTP API via API Gateway.
- Route: POST `/blog_generation`.
- Integration: Link to Lambda function.
- Deploy to stage (e.g., `dev`) → get public Invoke URL.

---

### Step 6: Create Amazon S3 Bucket
- Bucket name: `aws-bedrock-course-one`.
- Ensure this name is consistent in your Lambda function.

---

### Step 7: IAM Permissions for Lambda Role
- Grant Lambda role permissions to:
  - Invoke Bedrock models (`bedrock:InvokeModel`).
  - Put objects to S3 (`s3:PutObject`).
- Use `AdministratorAccess` for demo purposes, but apply granular permissions in production.

---

### Step 8: Test End-to-End Flow
- Use Postman or curl:
  ```json
  POST https://<api-id>.execute-api.<region>.amazonaws.com/dev/blog_generation
  {
    "blog_topic": "machine learning and generative AI"
  }
  ```
- Monitor CloudWatch logs for Lambda execution.
- Check S3 bucket for the blog text file.

---

## 3. Tech Stack

- **AWS Services**: Lambda, Bedrock, S3, API Gateway, CloudWatch, IAM
- **Language**: Python 3.12
- **Model Used**: `meta.llama2-13b-chat-v1` (via Amazon Bedrock)

---