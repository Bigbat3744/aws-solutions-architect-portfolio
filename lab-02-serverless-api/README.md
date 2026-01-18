# Lab 02 — Serverless API on AWS (API Gateway + Lambda + DynamoDB)

## 📌 Overview
This lab demonstrates how to build a fully serverless backend using:

- Amazon API Gateway  
- AWS Lambda  
- Amazon DynamoDB  
- IAM roles and permissions  

The architecture allows you to accept HTTP requests, process them with Lambda, and store data in DynamoDB — all without managing any servers.

---

## 🏗️ Architecture Diagram (Text Description)

```
Client → API Gateway → Lambda Function → DynamoDB Table
```

---

## 🎯 Objectives
- Create a DynamoDB table to store user data  
- Build a Lambda function to process API requests  
- Connect API Gateway to Lambda  
- Deploy a REST API endpoint  
- Test the full serverless workflow  
- Store and retrieve data from DynamoDB  

---

## 🛠️ Steps Performed

### 1. Created a DynamoDB Table
- Table name: `Users`  
- Partition key: `UserId` (String)  
- Default settings (on‑demand capacity)  

This table stores user records created through the API.

---

### 2. Created a Lambda Function
- Function name: `CreateUserFunction`  
- Runtime: Python 3.12  
- IAM role created with basic Lambda permissions  

Replaced the default code with:

```python
import json
import boto3
import uuid

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Users')

def lambda_handler(event, context):
    print("EVENT RECEIVED:", event)

    if 'body' not in event or event['body'] is None:
        return {
            'statusCode': 400,
            'body': json.dumps({'error': 'Missing request body'})
        }

    body = json.loads(event['body'])

    user_id = str(uuid.uuid4())

    item = {
        'UserId': user_id,
        'name': body.get('name'),
        'email': body.get('email')
    }

    table.put_item(Item=item)

    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'User created', 'UserId': user_id})
    }
```

### Added DynamoDB Permissions
Attached the policy:

- `AmazonDynamoDBFullAccess`

This allows Lambda to write to the `Users` table.

---

### 3. Created an API Gateway REST API
- API name: `UserAPI`  
- Created resource: `/users`  
- Created method: `POST`  
- Enabled **Lambda Proxy Integration**  
- Linked the method to `CreateUserFunction`  

---

### 4. Deployed the API
- Deployment stage: `prod`  
- Received a public Invoke URL, e.g.:

```
https://abc123.execute-api.us-east-1.amazonaws.com/prod/users
```

---

### 5. Tested the API

#### Test Body:
```json
{
  "name": "Olaitan",
  "email": "olaitan@example.com"
}
```

#### Expected Response:
```json
{
  "message": "User created",
  "UserId": "generated-uuid"
}
```

#### Verified in DynamoDB:
A new item appeared in the `Users` table with:

- UserId  
- name  
- email  

---

## ✅ Results
- Successfully built a serverless API  
- Lambda processed incoming requests  
- DynamoDB stored user data  
- API Gateway exposed a public REST endpoint  
- End‑to‑end serverless workflow completed  

---

## 🧠 What I Learned
- How API Gateway integrates with Lambda  
- How Lambda parses and processes JSON requests  
- How DynamoDB stores NoSQL data  
- How IAM roles allow secure service‑to‑service communication  
- How serverless architectures scale automatically  

---

## 🧹 Cleanup
Deleted:

- API Gateway API  
- Lambda function  
- DynamoDB table  

This ensures the environment remains Free Tier safe.

---

## 🧩 Skills Demonstrated
- Serverless architecture  
- REST API design  
- AWS Lambda development  
- DynamoDB NoSQL database usage  
- IAM permissions  
- Cloud debugging and testing  
