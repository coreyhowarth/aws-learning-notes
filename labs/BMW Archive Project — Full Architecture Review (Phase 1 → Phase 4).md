## The Big Goal

Build a production-style cloud application while learning real AWS engineering.

Final architecture so far:

```
User  
↓
Custom Domain  
↓
Route 53 DNS  
↓
CloudFront CDN  
↓
Private S3 Bucket  
↓
Frontend HTML/CSS/JavaScript  
↓
JavaScript fetch()  
↓
API Gateway  
↓
Lambda Function  
↓
IAM Role + Policies  
↓
DynamoDB Database
```

---

# Phase 1 — Static Website Deployment

Goal:

Deploy the website publicly using AWS.

Architecture:

```
User  
↓
S3 Bucket
```

Concepts learned:

### Amazon S3

Purpose:

```
Store website files
```

Stored files:

```
HTML
CSS
JavaScript
Images
```

Important concepts:

```
Bucket = Container for files
Object = Individual file
Static website hosting = Host website directly from S3
```

Problem discovered:

```
Access denied errors
```

Reason:

```
S3 permissions were blocking public access
```

Lesson:

```
Cloud resources require explicit permissions
```

---

# Phase 2 — Production Website Infrastructure

Goal:

Make website production-ready.

Architecture:

```
User  
↓
Domain Name  
↓
Route 53  
↓
CloudFront  
↓
Private S3 Bucket
```

Concepts learned:

### Amazon Route 53

Purpose:

```
Domain management
DNS records
Route traffic to AWS resources
```

---

### DNS

Definition:

```
Translates domain name into IP/location
```

Example:

```
awscoreyhowarth.com
```

---

### Nameservers

Purpose:

```
Tell internet where DNS records live
```

Problem discovered:

```
Domain registrar had wrong nameservers
```

Lesson:

```
DNS propagation takes time
```

---

### Amazon CloudFront

Purpose:

```
Cache files globally
Improve speed
Add HTTPS support
Protect S3 bucket
```

Architecture improvement:

Before:

```
Public S3 website
```

After:

```
Private S3 behind CloudFront
```

---

### SSL Certificate

Using:

AWS Certificate Manager

Purpose:

```
HTTPS encryption
Secure domain
```

Problem discovered:

```
Certificate stuck pending
```

Reason:

```
DNS validation records missing
```

Lesson:

```
SSL requires domain ownership validation
```

---

# Phase 3 — Frontend Becomes Dynamic

Goal:

Remove hardcoded HTML cards.

Old design:

```
Every BMW card manually written in HTML
```

Problem:

```
Impossible to scaleHard to manage hundreds of cars
```

New architecture:

```
JavaScript creates cards dynamically
```

---

Concepts learned:

### JavaScript Objects

Example:

```
const car = {  
	model: "BMW 328",  
	year: "1936"
}
```

Meaning:

```
Store related information together
```

---

### Arrays

Example:

```
const cars = [  {...},  {...}]
```

Meaning:

```
Store multiple objects
```

---

### Functions

Example:

```
function create
	CarCard(car)
```

Meaning:

```
Reusable block of code
```

---

### Loops

Example:

```
cars.forEach(car => {})
```

Meaning:

```
Repeat action for every object
```

---

### DOM Manipulation

Example:

```
document.getElementById()
```

Meaning:

```
JavaScript changes HTML after page loads
```

---

### Template Literals

Example:

```
${car.model}
```

Meaning:

```
Insert dynamic data into HTML
```

---

Result:

```
One function generates all cards automatically
```

---

# Phase 3.5 — Backend API

Goal:

Separate frontend from backend.

Old architecture:

```
Browser  
↓
JavaScript Array
```

New architecture:

```
Browser  
↓
API Gateway  
↓
Lambda
```

---

### AWS Lambda

Purpose:

```
Run backend code without managing servers
```

Concept:

```
Serverless computing
```

Meaning:

```
AWS runs code only when needed
```

---

### Amazon API Gateway

Purpose:

```
Public URL for backendRoutes requests into Lambda
```

Architecture:

```
Browser  
↓
API Gateway  ↓Lambda
```

---

### JSON APIs

Example:

```
[  {    "model": "BMW 328"  }]
```

Meaning:

```
Backend sends structured data to frontend
```

---

### fetch()

Example:

```
const response = await fetch(API_URL)
```

Meaning:

```
Browser requests backend data
```

---

# Phase 3.6 — CORS Security

Problem:

Browser blocked API request.

Error:

```
Cross-Origin Request Blocked
```

Reason:

Website:

```
https://awscoreyhowarth.com
```

API:

```
execute-api.amazonaws.com
```

Different origins.

---

### CORS

Meaning:

```
Cross-Origin Resource Sharing
```

Purpose:

```
Browser security protection
```

Without CORS:

```
Random websites could steal user data
```

Settings learned:

```
Allowed Origins
Allowed Methods
Allowed Headers
```

---

# Phase 4 — Real Database Backend

Goal:

Remove fake backend data.

Old Lambda:

```
const cars = [...]
```

Problem:

```
Backend data still hardcoded
```

New architecture:

```
Browser  
↓
API Gateway  
↓
Lambda  
↓
DynamoDB
```

---

### Amazon DynamoDB

Purpose:

```
Persistent cloud database
```

Equivalent:

```
Excel spreadsheet in AWS
```

Created table:

```
BMWModels
```

Primary key:

```
id
```

---

### Database terminology

Table:

```
Entire spreadsheet
```

Item:

```
One row
```

Attribute:

```
One column
```

Partition Key:

```
Unique identifier
```

Example:

```
bmw-507
bmw-i4
bmw-328
```

---

# IAM Security

Problem:

Lambda could not access database.

Reason:

```
AWS blocks everything by default
```

AWS security model:

```
Default = Deny everything
```

---

### IAM Role

Meaning:

```
Identity assigned to AWS service
```

Example:

```
Lambda has execution role
```

---

### IAM Policy

Meaning:

```
Permission rules attached to role
```

Attached:

```
AmazonDynamoDBFullAccess_v2
```

Allowed:

```
Read table
Write table
Delete table items
Scan table
Query table
```

---

# AWS SDK

Purpose:

Allow code to communicate with AWS.

Imported:

```
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
```

Meaning:

```
Bring AWS tools into JavaScript code
```

---

# ScanCommand

Code:

```
new ScanCommand({  TableName: "BMWModels"})
```

Meaning:

```
Get all records from database
```

Equivalent SQL:

```
SELECT * FROM BMWModels;
```

---

# Async Programming

### await

Example:

```
await docClient.send(command)
```

Meaning:

```
Pause until AWS finishes request
```

---

# Error Handling

Code:

```
try {}catch(error) {}
```

Purpose:

```
Catch failures instead of crashing
```

Logs sent to:

Amazon CloudWatch

Purpose:

```
Backend debugging
```

---

# Sorting Database Results

Problem:

Amazon DynamoDB Scan returns random order.

Solution:

```
cars.sort((a, b) => {  
	return Number(a.year) - Number(b.year)
})
```

Meaning:

```
Oldest BMW first
Newest BMW last
```

Lesson:

```
Databases do not guarantee order unless explicitly sorted
```

---

# Git + Version Control

Using:

[GitHub](https://github.com?utm_source=chatgpt.com)

Learned:

```
git add .git commit -m "message"git push
```

Purpose:

```
Save project history
Rollback broken code
Track changes over time
Portfolio visibility
```

---

# What You Actually Built

Not this:

```
Simple website
```

You built this:

```
Distributed cloud application
```

Architecture layers:

```
Presentation Layer
↓
Frontend Logic Layer
↓
API Layer↓Backend Compute Layer
↓
Security Layer
↓
Database Layer
↓
Monitoring Layer
```

---

# Technologies You Have Used

Amazon S3  
Amazon CloudFront  
Amazon Route 53  
AWS Certificate Manager  
AWS Lambda  
Amazon API Gateway  
Amazon DynamoDB  
Amazon CloudWatch  
[GitHub](https://github.com?utm_source=chatgpt.com)  
JavaScript  
HTML  
CSS  
JSON  
IAM Policies  
CORS  
DNS  
HTTPS/SSL