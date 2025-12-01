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

 PART - 1 
1. We will be Creating DynamoDB table and Lambda functions 
and we will creating two Lambda function 
one for GET
one for POST 
And we will configure to DynamoDB table 

STEP-1
1.Open DynamoDB in AWS 
2.Open table - Create table - Table name - Partition Key - Sort key - Create Table 
3.Open Lambda in AWS 
4.Create function
  Function name - studentData  
  Runtime - using python 3.12
  Execution role - using as existing role 
  Existing rule - lambdaDynamoDB-role
  CREATE FUNCTION
5.In Section Code 
  we need BOTO3 configuring DynamoDB
  Lambda function Code for getting data
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

    # If there are more items to scan, continue scanning until all items are retrieved
    while 'LastEvaluatedKey' in response:
        response = table.scan(ExclusiveStartKey=response['LastEvaluatedKey'])


    # Return the retrieved data
    return data
   We Need to Deploy
   then TEST it 
6. Configure test ivent 
   Event name - mytest
   SAVE IT 
7. Create another function in Lambda 
   Function name - insertStudentData 
   Runtime - using python 3.12
   Execution role - using as existing role
   Existing rule - lambdaDynamoDB-role
   CREATE FUNCTION
5.In Section Code
  Lambda function Code for inserting data 
  import json
  import boto3

  # Create a DynamoDB object using the AWS SDK
  dynamodb = boto3.resource('dynamodb')
  # Use the DynamoDB object to select our table
  table = dynamodb.Table('studentData')

  # Define the handler function that the Lambda service will use as an entry point
  def lambda_handler(event, context):
    # Extract values from the event object we got from the Lambda service and store in variables
    student_id = event['studentid']
    name = event['name']
    student_class = event['class']
    age = event['age']
    
    # Write student data to the DynamoDB table and save the response in a variable
    response = table.put_item(
        Item={
            'studentid': student_id,
            'name': name,
            'class': student_class,
            'age': age
        }
    )
    
    # Return a properly formatted JSON object
    return {
        'statusCode': 200,
        'body': json.dumps('Student data saved successfully!')
    }
   We Need to Deploy
   then TEST it
6. Configure test ivent 
   Event name - mytest
   Even JSON
   {
            'studentid': student_id,
            'name': name,
            'class': student_class,
            'age': age
        }

 
   SAVE IT

 

PART 1 IS COMPLETED 



