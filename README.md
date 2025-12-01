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





# **📌 Part 2: Create API Gateway for Lambda + S3 Static Website Hosting**

In this part, we create a REST API to trigger the Lambda functions and host a static website on S3 that interacts with DynamoDB.

---

## **✅ Step 1: Create REST API in API Gateway**

### **1. Open API Gateway**

* Open AWS Console → Search **API Gateway**
* Choose **Create API**
* Select **REST API**
* **API Name:** `student`
* **Endpoint Type:** Edge-Optimized
* Click **Create API**

---

## **📌 Step 1.1: Create GET Method**

1. Select **Resources**
2. Click **Create Method**
3. Choose **GET**
4. **Integration Type:** Lambda
5. Select Region
6. Choose Lambda Function: `functiongetStudent`
7. Click **Create Method**

---

## **📌 Step 1.2: Create POST Method**

1. Click **Create Method**
2. Choose **POST**
3. **Integration Type:** Lambda
4. Choose Lambda Function: `insertStudentData`
5. Click **Create Method**

---

## **📌 Step 1.3: Enable CORS**

* Open **Resources**
* Click **Enable CORS**
* Allow Methods → **GET, POST**
* Save

---

## **📌 Step 1.4: Deploy API**

1. Click **Deploy API**
2. **Stage:** New Stage
3. **Stage Name:** `prod`
4. Deploy

📌 **Copy the Invoke URL**
You will paste this in `scripts.js`.

---

# **✅ Step 2: S3 Static Website Hosting**

## **📌 Step 2.1: Create S3 Bucket**

1. Open S3
2. Click **Create Bucket**
3. Enter unique bucket name
4. Click **Create Bucket**

---

## **📌 Step 2.2: Upload `index.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Data</title>
    <style>
        body {
            background-color: #f0f0f0;
            color: #333;
            font-family: Arial, sans-serif;
        }
        h1 { color: #007bff; }
        .container {
            max-width: 600px;
            margin: auto;
            padding: 20px;
            background: #fff;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input[type="text"], input[type="submit"] {
            width: 100%;
            padding: 10px;
            margin: 5px 0;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        input[type="submit"] {
            background: #007bff;
            color: #fff;
        }
        input[type="submit"]:hover { background: #0056b3; }
        table { width: 100%; border-collapse: collapse; }
        th, td {
            padding: 8px;
            border-bottom: 1px solid #ddd;
        }
        th { background: #f2f2f2; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Save and View Student Data</h1>
        <label>Student ID:</label>
        <input type="text" id="studentid">

        <label>Name:</label>
        <input type="text" id="name">

        <label>Class:</label>
        <input type="text" id="class">

        <label>Age:</label>
        <input type="text" id="age">

        <br>
        <input type="submit" id="savestudent" value="Save Student Data">
        <p id="studentSaved"></p>

        <input type="submit" id="getstudents" value="View all Students">
        <br><br>

        <table id="studentTable">
            <thead>
                <tr>
                    <th>Student ID</th>
                    <th>Name</th>
                    <th>Class</th>
                    <th>Age</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>

    <script src="scripts.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.0/jquery.min.js"></script>
</body>
</html>
```

---

## **📌 Step 2.3: Create & Upload `scripts.js`**

Replace `API_ENDPOINT` with your Invoke URL.

```javascript
// Add your API endpoint here
var API_ENDPOINT = "PASTE_YOUR_API_ENDPOINT_HERE";

// POST request to save student data
document.getElementById("savestudent").onclick = function(){
    var inputData = {
        "studentid": $('#studentid').val(),
        "name": $('#name').val(),
        "class": $('#class').val(),
        "age": $('#age').val()
    };

    $.ajax({
        url: API_ENDPOINT,
        type: 'POST',
        data: JSON.stringify(inputData),
        contentType: 'application/json; charset=utf-8',

        success: function () {
            document.getElementById("studentSaved").innerHTML = "Student Data Saved!";
        },
        error: function () {
            alert("Error saving student data.");
        }
    });
}

// GET request to fetch students
document.getElementById("getstudents").onclick = function(){  
    $.ajax({
        url: API_ENDPOINT,
        type: 'GET',
        contentType: 'application/json; charset=utf-8',

        success: function (response) {
            $('#studentTable tbody').empty();

            $.each(response, function(i, data) {          
                $("#studentTable").append(`
                    <tr>
                        <td>${data.studentid}</td>
                        <td>${data.name}</td>
                        <td>${data.class}</td>
                        <td>${data.age}</td>
                    </tr>
                `);
            });
        },
        error: function () {
            alert("Error retrieving student data.");
        }
    });
}
```

---

## **📌 Step 2.4: Enable Static Website Hosting**

1. Open **Bucket Properties**
2. Scroll to **Static Website Hosting**
3. Click **Edit**
4. Enable Static Website Hosting
5. Index Document → `index.html`
6. Save

---

# **📌 Part 3: Add CloudFront in Front of S3 (HTTPS Enabled)**

To provide **HTTPS security**, we place CloudFront in front of the S3 website.

---

## **✅ Step 3.1: Create CloudFront Distribution**

1. Open **CloudFront**
2. Click **Create Distribution**
3. **Origin Domain:** Select your S3 bucket
4. **Origin Access:** Create *New Origin Access Control (OAC)*
5. **Default Root Object:** `index.html`
6. **WAF:** Do not enable
7. Click **Create Distribution**

---

## **📌 Step 3.2: Copy & Apply S3 Bucket Policy**

CloudFront will provide a bucket policy.
Copy it → Go to **S3 Bucket → Permissions → Bucket Policy** → Paste → Save.

---

## **📌 Step 3.3: Get CloudFront URL**

1. Open CloudFront distribution
2. Under **General**, copy:

### **➡️ Distribution Domain Name (HTTPS URL)**

This is your **secure website link**.

Your S3 website is now served over **HTTPS** using CloudFront.

---

# 🎉 **SETUP COMPLETE — PROJECT READY FOR DEPLOYMENT**

All 3 parts (DynamoDB + Lambda, API Gateway + S3 Hosting, CloudFront ) 

 


 

