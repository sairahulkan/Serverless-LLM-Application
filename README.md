# AWS Lambda Transcript Summarization

This project utilizes AWS Lambda to process transcripts stored in S3, extract the text content, and generate summaries using Amazon Bedrock.


## Features

- **AWS Lambda**:
  - Processes S3 bucket events to read JSON transcript files.
  - Extracts text content from AWS Textract results.
  - Uses Amazon Bedrock to generate summaries from transcripts.

- **Amazon Bedrock**:
  - Utilizes pre-trained models (`amazon.titan-text-express-v1` and `amazon.titan-text-lite-v1`) for text generation and summarization.

## Prerequisites

- AWS Account with appropriate permissions.
- S3 Buckets for input and output.
- AWS Lambda configured with the necessary IAM roles and policies.
- Amazon Bedrock access.

## Installation

1. **Clone the Repository**:

    ```bash
    git clone https://github.com/your-username/aws-lambda-transcript-summarization.git
    cd aws-lambda-transcript-summarization
    ```

2. **Install Dependencies**:

    Ensure you have the necessary Python packages installed. You can use a virtual environment:

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ```

3. **Deploy the Lambda Function**:

    - Package the code and dependencies.
    - Deploy to AWS Lambda using the AWS Management Console, AWS CLI, or an infrastructure as code tool like Terraform or CloudFormation.

## Configuration

- **AWS S3**:
  - Set up an input bucket to store JSON transcript files.
  - Set up an output bucket to store the generated summaries.

- **Lambda Environment Variables**:
  - Configure the Lambda function with environment variables for S3 bucket names and any other required configuration.

## Usage

1. **Trigger the Lambda Function**:

    Upload a JSON transcript file to the input S3 bucket. This will trigger the Lambda function.

2. **Lambda Execution**:

    The Lambda function will:
    - Read the JSON file from the S3 input bucket.
    - Extract the transcript content.
    - Generate a summary using Amazon Bedrock.
    - Save the summary to the output S3 bucket.

## Functions

### `lambda_handler(event, context)`

Main entry point for the Lambda function. Processes S3 bucket events and orchestrates the transcript extraction and summarization.

### `extract_transcript_from_textract(file_content)`

Parses the JSON transcript file and extracts the text content.

### `bedrock_summarisation(transcript)`

Generates a summary from the extracted transcript using Amazon Bedrock.

## Example Usage

```python
import boto3
import json

bedrock_runtime = boto3.client('bedrock-runtime', region_name='us-east-1')
prompt = "Write a summary of Tempe."
kwargs = {
    "modelId": "amazon.titan-text-lite-v1",
    "contentType": "application/json",
    "accept": "*/*",
    "body": json.dumps(
        {
            "inputText": prompt,
            "textGenerationConfig": {
                "maxTokenCount": 500,
                "temperature": 0.7,
                "topP": 0.9
            }
        }
    )
}

response = bedrock_runtime.invoke_model(**kwargs)
response_body = json.loads(response.get('body').read())

generation = response_body['results'][0]['outputText']
print(generation)
print(json.dumps(response_body, indent=4))


