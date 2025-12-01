# Serverless-Web-Application-on-AWS

**Project type:** Serverless Web Application  
**Tech & AWS services:** Python, AWS Lambda, API Gateway, Amazon DynamoDB, S3 (static hosting), CloudFront, IAM .

## 🔎 Project Summary
Developed a serverless web application using AWS Lambda, API Gateway, DynamoDB, and S3. Backend logic is implemented in **Python** with an event-driven workflow to reduce manual effort. The app provides high scalability, secure API integration, zero server maintenance, and cost-efficient performance — resulting in a fast, reliable, fully managed cloud-native application.

 

## 🏗 Architecture  
1. **Clients** (Browser / Mobile) -> request to  
2. **Amazon CloudFront** -> routes to **S3** for static assets (frontend) or to  
3. **API Gateway** -> triggers **AWS Lambda (Python)** -> performs business logic and CRUD on **DynamoDB**.  
4. **CloudWatch** collects logs & metrics; **IAM** roles provide least-privilege access.
5. <img width="2010" height="880" alt="serverless-lambda-dynamodb-min" src="https://github.com/user-attachments/assets/65d56edf-62bf-4659-8dd2-79120091865d" />

Here is a **clean, professional, GitHub-ready README (Part-1)** based on your steps.
You can copy–paste directly into your repository.

---

# **📌 Part 1: Creating DynamoDB Table & Lambda Functions (GET & POST)**

In this part, we create a DynamoDB table and two AWS Lambda functions:

* **GET Lambda** → Fetch all student records
* **POST Lambda** → Insert a new student record

---

## **✅ Step 1: Create DynamoDB Table**

1. Open **AWS Console** → Search **DynamoDB**
2. Go to **Tables** → Click **Create table**
3. Enter details:

   * **Table Name:** `studentData`
   * **Partition Key:** `studentid` (String)
   * **Sort Key:** Optional (if needed)
4. Click **Create Table**

---

## **✅ Step 2: Create Lambda Function for GET API**

### **1. Open AWS Lambda**

* Click **Create Function**

### **2. Enter Details**

* **Function Name:** `studentData`
* **Runtime:** Python 3.12
* **Execution Role:** Use existing role
* **Existing Role:** `lambdaDynamoDB-role`
* Click **Create Function**

---

## **🧾 Lambda GET Function Code**

```python
import json
import boto3

def lambda_handler(event, context):
    # Initialize a DynamoDB resource object for the specified region
    dynamodb = boto3.resource('dynamodb', region_name='us-east-2')

    # Select the DynamoDB table named 'studentData'
    table = dynamodb.Table('studentData')

    # Scan the table to retrieve all items
    response = table.scan()
    data = response['Items']

    # Continue scanning if more data exists
    while 'LastEvaluatedKey' in response:
        response = table.scan(ExclusiveStartKey=response['LastEvaluatedKey'])
        data.extend(response['Items'])

    return data
```

### **3. Deploy the function**

Click **Deploy**

### **4. Test the function**

* Go to **Test**
* **Event Name:** `mytest`
* Save
* Click **Test**

---

## **✅ Step 3: Create Lambda Function for POST API**

### **1. Create New Lambda Function**

* **Function Name:** `insertStudentData`
* **Runtime:** Python 3.12
* **Execution Role:** Use existing role
* **Role:** `lambdaDynamoDB-role`
* Click **Create Function**

---

## **🧾 Lambda POST Function Code**

```python
import json
import boto3

# Create a DynamoDB resource object
dynamodb = boto3.resource('dynamodb')

# Connect to our DynamoDB table
table = dynamodb.Table('studentData')

def lambda_handler(event, context):
    # Extract values from event
    student_id = event['studentid']
    name = event['name']
    student_class = event['class']
    age = event['age']
    
    # Insert data into DynamoDB
    response = table.put_item(
        Item={
            'studentid': student_id,
            'name': name,
            'class': student_class,
            'age': age
        }
    )
    
    return {
        'statusCode': 200,
        'body': json.dumps('Student data saved successfully!')
    }
```

### **2. Deploy the function**

Click **Deploy**

---

## **🧪 Test Event JSON (for POST)**

Use the following JSON:

```json
{
  "studentid": "101",
  "name": "Rahul",
  "class": "10",
  "age": 15
}
```

* **Event Name:** `mytest`
* Save → Test

---

# ✅ **PART 1 COMPLETED**

 

