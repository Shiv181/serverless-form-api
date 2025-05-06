
# Serverless Form API (Lambda)


## Overview

A cost-effective, fully serverless AWS Lambda function (with API Gateway) that ingests form data into an RDS database without any servers to manage.

## Features

* **Serverless** architecture using AWS Lambda and API Gateway
* **RDS Integration** to store form submissions in a managed relational database
* **Public Function URL** for easy form integration
* **Environment-Driven Configuration** for secure database credentials
* **Built-in Testing** with sample success and error events

## Architecture

```text
User Form → API Gateway → AWS Lambda → Amazon RDS
```

## Prerequisites

* AWS account with permissions to create Lambda functions, API Gateway, and RDS instances
* Python 3.10 runtime (used for Lambda)
* ZIP utility to package code and dependencies

## Repository Structure

```
├── lambda_function.py        # Main function handler
├── requirements.txt         # Python dependencies
├── layer.zip                # Optional Lambda layer with libraries
├── .env.example             # Sample environment variables file
└── images/                  # Screenshots used in this README
    ├── project-card.png
    ├── create-lambda-function.png
    ├── configure-timeout.png
    ├── configure-env-vars.png
    ├── configure-vpc.png
    ├── test-event-success.png
    ├── test-event-failure.png
    └── test-results.png
```

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/lambda-form-api.git
cd lambda-form-api
```

### 2. Package Dependencies (Optional Layer)

If you have external Python packages, install them into a folder and zip:

```bash
pip install -r requirements.txt -t python
zip -r layer.zip python
```

## AWS Lambda Setup

### 3. Create Lambda Function

1. Open the AWS Lambda console.
2. Click **Create function** and choose **Author from scratch**.
3. Enter a descriptive name (e.g., `form-api-handler`).
4. Select **Python 3.10** runtime.
5. Under **Function URL**, enable a public URL and disable authentication.

![Create Lambda Function](images/create-lambda-function.png)

### 4. Upload Code & Layer

* In the **Code** tab, upload your `lambda_function.py` (as a ZIP if larger).
* If using a layer, upload `layer.zip` under **Layers**.

### 5. Configure Timeout

Increase the function timeout to 30 seconds to allow database connections:

1. Under **Configuration → General configuration**, click **Edit**.
2. Set **Timeout** to `30 sec`.

![Configure Timeout](images/configure-timeout.png)

### 6. Environment Variables

Add environment variables matching your `.env` settings:

| Key          | Value                  |
| ------------ | ---------------------- |
| DB\_HOST     | `<your-rds-endpoint>`  |
| DB\_PORT     | `5432`                 |
| DB\_NAME     | `<your-database-name>` |
| DB\_USER     | `<your-db-username>`   |
| DB\_PASSWORD | `<your-db-password>`   |

![Configure Environment Variables](images/configure-env-vars.png)

### 7. VPC & Network Connection

If your RDS instance is in a VPC, attach the Lambda function to the same VPC and subnets, and configure security groups to allow inbound connections on the database port.

![Configure VPC Connection](images/configure-vpc.png)

## Testing

### 8. Create Test Events

#### Success Case

```json
{
  "httpMethod": "POST",
  "body": "{\"name\":\"Test User\",\"email\":\"test@example.com\",\"subject\":\"Hello\",\"message\":\"This is a test.\"}"
}
```

![Test Event Success](images/test-event-success.png)

#### Failure Case (Missing Subject)

```json
{
  "httpMethod": "POST",
  "body": "{\"name\":\"Test User\",\"email\":\"test@example.com\",\"message\":\"Missing subject field.\"}"
}
```

![Test Event Failure](images/test-event-failure.png)

### 9. Run Tests

* Deploy your latest code by clicking **Deploy**.
* Select each test event and click **Test**.

![Test Results](images/test-results.png)

## Deployment & Usage

1. Copy the **Function URL** displayed on the Lambda function page.
2. In your frontend form, set the `action` to this URL and method to `POST`.
3. Submit the form to see entries appear in your RDS database.

```html
<form action="https://<your-function-url>" method="POST">
  <!-- form fields -->
</form>
```

