# Serverless AWS Chatbot

This project is a simple, serverless AI chatbot powered by Amazon Bedrock. It provides a web-based user interface that communicates with a backend hosted on AWS Lambda via Amazon API Gateway.

## Architecture

*   **Frontend**: A static HTML/CSS/JS single-page application (`index.html`). It can be hosted on Amazon S3.
*   **API Layer**: Amazon API Gateway handles HTTP POST requests from the frontend and routes them to the backend Lambda function.
*   **Backend**: An AWS Lambda function (`lambda_function.py`) written in Python. It processes the conversation history and the new user message.
*   **AI Model**: Amazon Bedrock is used as the foundational AI service. The Lambda function specifically invokes the `amazon.titan-text-express-v1` model to generate responses.

## Project Files

*   `index.html`: The chatbot's user interface. It contains the layout, styling, and JavaScript logic to send requests to the API Gateway.
*   `lambda_function.py`: The AWS Lambda backend code. It handles CORS, formats the prompt using the entire conversation history, invokes the Amazon Bedrock Titan model, and returns the response.
*   `s3-bucket-policy.json`: An example S3 bucket policy required to make your bucket public for static website hosting.

## Setup Instructions

### 1. Backend (AWS Lambda & Amazon Bedrock)
1. Ensure you have enabled access to the **Amazon Titan Text Express** model in the Amazon Bedrock console in your desired AWS region (e.g., `us-east-1`).
2. Create a new AWS Lambda function using Python 3.x.
3. Copy the contents of `lambda_function.py` into your Lambda function.
4. Give your Lambda function's execution role the necessary permissions to call `bedrock:InvokeModel`. Example IAM policy statement:
   ```json
   {
       "Effect": "Allow",
       "Action": "bedrock:InvokeModel",
       "Resource": "*"
   }
   ```
5. Ensure the region defined in `lambda_function.py` (`us-east-1`) matches the region where you enabled model access in Bedrock.

### 2. API Gateway
1. Create a new API in Amazon API Gateway.
2. Create a new method (e.g., `POST` on a `/chat` route) integrated with your Lambda function.
3. **Enable CORS** on the API resource to allow requests from your frontend's domain.
4. Deploy the API and note the endpoint URL.
5. Open `index.html` and update the `fetch` request URL (around line 111) with your new API Gateway endpoint URL:
   ```javascript
   const response = await fetch('YOUR_API_GATEWAY_INVOKE_URL', {
   ```

### 3. Frontend (Amazon S3)
1. Create an Amazon S3 bucket.
2. Enable **Static website hosting** on the bucket.
3. Adjust the bucket's "Block Public Access" settings to allow public access.
4. Apply the bucket policy from `s3-bucket-policy.json` (replacing `your-bucket-name` with your actual bucket name) to make the contents publicly readable.
5. Upload the modified `index.html` file to your S3 bucket.
6. Open the S3 static website endpoint URL in your browser to interact with your newly deployed AI chatbot.
