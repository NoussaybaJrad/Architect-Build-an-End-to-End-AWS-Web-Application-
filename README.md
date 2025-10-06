# Architect & Build an End-to-End AWS Web Application

## Description

This project demonstrates how to design and implement a **fully functional serverless web application** on AWS. The web app allows a user to input a base and an exponent, calculates the result using a Lambda function, stores the result in DynamoDB, and returns the answer to the browser in real time.  

**Key AWS Services Used:**  
- **AWS Amplify** – Hosts the web application  
- **AWS Lambda** – Performs the math calculation using Python (`math.pow`)  
- **API Gateway** – Triggers the Lambda function from the front-end  
- **DynamoDB** – Stores the results of the calculations  
- **IAM** – Provides secure permissions for Lambda, API Gateway, and DynamoDB integration  

---

## Architecture Overview

1. **User Input** – The user enters a base and exponent on the website hosted on Amplify.  
2. **API Trigger** – When the user clicks "Calculate," the front-end calls **API Gateway**.  
3. **Lambda Execution** – API Gateway triggers the **Python Lambda function**, which:  
   - Extracts the numbers from the event object  
   - Computes the result with `math.pow(base, exponent)`  
   - Stores the result in **DynamoDB**  
   - Returns the result in a JSON response  
4. **Database Storage** – DynamoDB saves each calculation along with metadata (timestamp, input values).  
5. **Response to User** – The result is returned through API Gateway and displayed in the browser.  

---

## Environment and Languages Used

- **AWS Console Management** (latest version)  
- **VS Code** (text editor)  
- **Python** (Lambda runtime)  
- **HTML/JavaScript** for front-end (Amplify-hosted website)  

---

## File Structure & How the Code Works

### Front-end (`index.html`)
- Captures user input (base and exponent)  
- Sends a POST request to API Gateway endpoint in JSON format  

### API Gateway
- Receives JSON from front-end  
- Triggers Lambda function with input payload  

### Database (`DynamoDB Table`)
- Each calculation is saved with a unique ID, input values, result, and timestamp  

### IAM
- Lambda role allows `dynamodb:PutItem`  
- API Gateway role triggers Lambda  
- Minimal permissions applied for security best practices  

### Lambda Function (`lambda_function.py`)
```python
# import the JSON utility package
import json
# import the Python math library
import math

# import the AWS SDK (for Python the package name is boto3)
import boto3
# import two packages to help us with dates and date formatting
from time import gmtime, strftime

# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')
# use the DynamoDB object to select our table
table = dynamodb.Table('PowerOfMathDatabase2')
# store the current time in a human readable format in a variable
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

# define the handler function that the Lambda service will use as entry point
def lambda_handler(event, context):
    
    # extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

    # write result and time to the DynamoDB table using the object we instantiated
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime': now
        })

    # return a properly formatted JSON object
    return {
        'statusCode': 200,
        'body': json.dumps('Your result is ' + str(mathResult))
    }
## Full Demo & Screenshots
For a complete walkthrough, including **full-resolution screenshots and screen recordings**, visit my Google Drive folder:
[View Project Media](https://drive.google.com/drive/folders/1BCo6yIsPbxE7umx8B8FvWNRZCBDZqh7w?usp=sharing)
