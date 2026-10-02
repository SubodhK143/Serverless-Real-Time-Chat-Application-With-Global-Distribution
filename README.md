# 🚀 Serverless Real-Time Chat Application with Global Distribution

A **serverless real-time chat application** built on AWS that enables users to send and receive messages instantly using **WebSocket communication**, without managing traditional application servers.

The project demonstrates how AWS managed services can be combined to build a scalable and globally accessible real-time application.

---

## 📌 Project Overview

This project implements a real-time chat architecture using:

* 🔌 **Amazon API Gateway WebSocket API** for two-way communication
* ⚡ **AWS Lambda** for serverless backend processing
* 🗄️ **Amazon DynamoDB** for storing WebSocket connection IDs
* 📦 **Amazon S3** for hosting static application files
* 🌍 **Amazon CloudFront** for global content distribution
* ☁️ **AWS CloudFormation** for infrastructure provisioning
* 🔐 **AWS IAM** for permissions and access control

The application manages the complete WebSocket connection lifecycle:

```text
User Connects
     ↓
$connect
     ↓
Lambda
     ↓
Store Connection ID
     ↓
DynamoDB
```

When a user sends a message:

```text
User
 ↓
WebSocket API
 ↓
sendmessage Route
 ↓
Lambda
 ↓
DynamoDB
 ↓
API Gateway Management API
 ↓
Connected Users
```

When the user disconnects:

```text
User Disconnects
       ↓
   $disconnect
       ↓
     Lambda
       ↓
Remove Connection ID
       ↓
    DynamoDB
```

---

# 🏗️ Architecture

```text
                         🌍 USERS
                            │
                            │ WebSocket
                            ▼
                  ┌──────────────────────┐
                  │   Amazon API Gateway │
                  │     WebSocket API    │
                  └──────────┬───────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
          $connect       sendmessage    $disconnect
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                    ┌─────────────────┐
                    │   AWS Lambda    │
                    │                 │
                    │ ConnectHandler  │
                    │ DisconnectHandler│
                    │ SendMessageHandler│
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Amazon DynamoDB  │
                    │                 │
                    │ Connection IDs  │
                    └────────┬────────┘
                             │
                             ▼
                 API Gateway Management API
                             │
                             ▼
                    💬 Connected Clients


              STATIC WEB APPLICATION
                         │
                         ▼
                    Amazon S3
                         │
                         ▼
                  Amazon CloudFront
                         │
                         ▼
                    🌍 Global Users
```

---

# ☁️ AWS Services

| Service                      | Purpose                                         |
| ---------------------------- | ----------------------------------------------- |
| 🔌 **API Gateway WebSocket** | Provides real-time two-way communication        |
| ⚡ **AWS Lambda**             | Executes backend logic without managing servers |
| 🗄️ **DynamoDB**             | Stores active WebSocket connection IDs          |
| 📦 **Amazon S3**             | Stores static web application files             |
| 🌍 **CloudFront**            | Distributes static content globally             |
| ☁️ **CloudFormation**        | Provisions AWS infrastructure                   |
| 🔐 **IAM**                   | Provides permissions and access control         |

---

# ✨ Key Features

* ⚡ Real-time messaging
* 🔌 Persistent WebSocket connections
* 🖥️ Serverless backend
* 🌍 Global content distribution
* 📈 Scalable AWS architecture
* 💾 DynamoDB connection management
* 🔄 Automated connection lifecycle handling
* ☁️ CloudFormation-based infrastructure
* 🔐 IAM-based access control
* 💰 Pay-per-use serverless architecture

---

# 📋 Prerequisites

Before starting the project, make sure you have:

* AWS Account
* AWS Management Console access
* Basic knowledge of:

  * AWS Lambda
  * API Gateway
  * DynamoDB
  * CloudFormation
  * Amazon S3
  * Amazon CloudFront
* AWS CLI
* AWS SAM CLI *(optional)*
* Node.js / npm
* `wscat`

Install `wscat`:

```bash
npm install -g wscat
```

---

# 📂 Project Structure

```text
serverless-real-time-chat/
│
├── README.md
│
├── cloudformation/
│   └── template.yaml
│
├── lambda/
│   ├── connect-handler/
│   ├── disconnect-handler/
│   └── send-message-handler/
│
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
│
└── screenshots/
    ├── cloudformation.png
    ├── api-gateway.png
    ├── dynamodb.png
    └── chat.png
```

> Update the structure above according to the actual files in your repository.

---

# 🚀 Implementation

## 1️⃣ Deploy CloudFormation Stack

The project uses an AWS CloudFormation template to provision the backend resources.

### Steps

1. Sign in to the **AWS Management Console**.
2. Open **CloudFormation**.
3. Select **Create stack**.
4. Choose **With new resources (standard)**.
5. Upload the CloudFormation template.
6. Enter the stack name:

```text
serverless-chat
```

7. Continue through the configuration.
8. Acknowledge the IAM resource creation.
9. Choose **Submit**.

Wait until the stack status becomes:

```text
CREATE_COMPLETE
```

The CloudFormation stack creates the Lambda functions and DynamoDB resources required by the application.

---

# 2️⃣ Create WebSocket API

Open **Amazon API Gateway**.

Select:

```text
Create API
        ↓
WebSocket API
        ↓
Build
```

### API Configuration

**API Name**

```text
serverless-chatapp-api
```

**Route Selection Expression**

```text
$request.body.action
```

The route selection expression determines which WebSocket route should handle an incoming message.

---

# 3️⃣ Configure WebSocket Routes

Create the following routes:

```text
$connect
$disconnect
$default
sendmessage
```

### Route Responsibilities

| Route         | Function                           |
| ------------- | ---------------------------------- |
| `$connect`    | Handles a new WebSocket connection |
| `$disconnect` | Handles a disconnected client      |
| `$default`    | Handles unmatched requests         |
| `sendmessage` | Handles chat messages              |

---

# 4️⃣ Configure Lambda Integrations

Attach Lambda functions to the WebSocket routes.

### `$connect`

```text
$connect
   ↓
serverless-chat-ConnectHandler
```

### `$disconnect`

```text
$disconnect
   ↓
serverless-chat-DisconnectHandler
```

### `sendmessage`

```text
sendmessage
   ↓
serverless-chat-SendMessageHandler
```

The Lambda integrations allow API Gateway to invoke the appropriate backend function for each event.

---

# 5️⃣ Deploy the WebSocket API

Create a deployment stage.

Example:

```text
production
```

After deployment, API Gateway provides a WebSocket endpoint similar to:

```text
wss://<api-id>.execute-api.<region>.amazonaws.com/production/
```

For example:

```text
wss://xxxxxxxxxx.execute-api.ap-south-1.amazonaws.com/production/
```

---

# 🧪 6️⃣ Test the Application

Use `wscat` to connect to the WebSocket API.

```bash
wscat -c wss://<api-id>.execute-api.ap-south-1.amazonaws.com/production/
```

A successful connection should display:

```text
Connected (press CTRL+C to quit)
```

Open another terminal and establish another connection:

```bash
wscat -c wss://<api-id>.execute-api.ap-south-1.amazonaws.com/production/
```

---

# 💬 7️⃣ Send a Message

Send a JSON message:

```json
{
  "action": "sendmessage",
  "message": "Hello, everyone!"
}
```

Because the route selection expression is:

```text
$request.body.action
```

API Gateway identifies:

```text
action = sendmessage
```

and invokes the `sendmessage` route.

The Lambda function then retrieves the active connection IDs from DynamoDB and sends the message to connected clients through the API Gateway Management API.

---

# 🔄 WebSocket Lifecycle

## Client Connection

```text
Client
  │
  ▼
$connect
  │
  ▼
Connect Lambda
  │
  ▼
Connection ID
  │
  ▼
DynamoDB
```

## Message Flow

```text
Client
  │
  │ {"action":"sendmessage"}
  ▼
API Gateway
  │
  ▼
sendmessage
  │
  ▼
SendMessage Lambda
  │
  ▼
DynamoDB
  │
  │ Retrieve Connection IDs
  ▼
API Gateway Management API
  │
  ▼
Connected Clients
```

## Client Disconnection

```text
Client
  │
  ▼
$disconnect
  │
  ▼
Disconnect Lambda
  │
  ▼
Remove Connection ID
  │
  ▼
DynamoDB
```

---

# 🌍 Global Distribution

The static frontend can be stored in Amazon S3.

CloudFront can then distribute the frontend globally.

```text
                 🌍 USER
                    │
                    ▼
             Amazon CloudFront
                    │
                    ▼
                Amazon S3
                    │
                    ▼
          Static Web Application
```

This architecture allows the frontend content to be delivered through CloudFront edge locations around the world.

---

# 🔐 Security Considerations

For a production implementation:

* Use IAM roles instead of hardcoded AWS credentials.
* Follow the principle of least privilege.
* Restrict IAM permissions to required resources.
* Avoid exposing sensitive configuration files.
* Do not commit AWS access keys or secrets to GitHub.
* Use secure WebSocket connections (`wss://`).
* Enable appropriate CloudWatch logging.
* Review S3 bucket access policies.
* Implement authentication and authorization where required.

---

# 📊 Monitoring & Troubleshooting

AWS CloudWatch can be used to monitor Lambda execution and application activity.

Useful areas include:

```text
CloudWatch
   │
   ├── Lambda Logs
   ├── API Gateway Logs
   └── Application Metrics
```

Useful AWS CLI commands:

```bash
aws cloudformation describe-stacks
```

```bash
aws dynamodb list-tables
```

```bash
aws lambda list-functions
```

---

# 🧹 Cleanup

To avoid unnecessary AWS charges, delete the resources after completing testing.

### Delete WebSocket API

```text
API Gateway
    ↓
APIs
    ↓
Select WebSocket API
    ↓
Actions
    ↓
Delete
```

### Delete CloudFormation Stack

```text
CloudFormation
    ↓
Stacks
    ↓
serverless-chat
    ↓
Delete
```

Also verify that any additional resources created outside CloudFormation are removed when no longer required.

---

# 🧠 Skills Demonstrated

This project demonstrates hands-on experience with:

* AWS API Gateway
* WebSocket APIs
* AWS Lambda
* Amazon DynamoDB
* Amazon S3
* Amazon CloudFront
* AWS CloudFormation
* AWS IAM
* Serverless Architecture
* Real-Time Communication
* AWS CLI
* WebSocket Testing
* Cloud Architecture

---

# 🎯 Interview Explanation

### How I would explain this project

> I built a serverless real-time chat application using Amazon API Gateway WebSocket APIs, AWS Lambda and DynamoDB. API Gateway manages the WebSocket connections, while Lambda functions handle connection, disconnection and message events. Active connection IDs are stored in DynamoDB. When a client sends a message, the SendMessage Lambda retrieves the connected clients and uses the API Gateway Management API to deliver the message. I also used S3 and CloudFront for static application hosting and global content distribution. CloudFormation can be used to provision the backend infrastructure in a repeatable way.

---

# 📚 AWS Reference

The implementation was based on the AWS WebSocket chat application tutorial:

**Tutorial:** Create a WebSocket chat app with a WebSocket API, Lambda and DynamoDB.

The project source material credits the AWS tutorial as the foundation for the base architecture.

---

# 👨‍💻 Author

## Subodh Kumar

**AWS Cloud Support Engineer | AWS | DevOps | Cloud Computing**

### 🔗 Connect With Me

* 🐙 **GitHub:** [github.com/SubodhK143](https://github.com/SubodhK143)
* 💼 **LinkedIn:** [Subodh Kumar — AWS Certified](https://www.linkedin.com/in/subodh-kumar-aws-certified/)

---

## ⭐ Support

If you find this project useful, consider giving the repository a ⭐ **Star** on GitHub.

---

### 📌 Project

**Serverless Real-Time Chat Application with Global Distribution**

Built with ❤️ using AWS Serverless Services.
