# Project: Power of Math
---
# Cloud-Based Web Application | AWS Serverless Architecture
---

## **Description:**
Developed a fully serverless web application, Power of Math, that performs mathematical operations and provides a responsive user interface for users to interact with math-based features. The application is built using modern AWS services, ensuring scalability, cost-efficiency, and high availability.

---
## **Technologies Used:**
- **AWS Amplify** : Frontend hosting and CI/CD.
  
- **AWS Lambda** : Backend logic for processing math operations.
 
- **AWS API Gateway** : RESTful APIs to handle frontend-backend communication.
  
- **AWS IAM** : Managed access control and security.
 
- **AWS DynamoDB** : NoSQL database for storing user data and operation history.
---

## **Key Highlights:**
- Designed and deployed a fully serverless, scalable architecture.

- Implemented secure API endpoints using IAM and API Gateway.

- Achieved seamless frontend-backend integration using Amplify and Lambda.

- Optimized DynamoDB queries for fast data access and storage.

- Gained hands-on experience in cloud-native development and AWS best practices.

  ---
  ## **Project Setup Steps**
  ## **step 1: create index.zip folder**
  - Create a index.html file using the following  code.
  - now compress it to zip folder.
  
 ```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>To the Power of Math!</title>
</head>

<body>
    To the Power of Math!
</body>
</html>
```
## **step 2: Deploying and hosting a web page using AWS Amplify**
- Go to AWS management console and search for "Amplify".
- Go to AWS Amplify & configure following details:
- **Start Building with Amplify :**  Deploy without Git
- **Start manual deployment :**
     -    App Name -PowerOfMath
     -    Branch Name - dev
- **Method :** Drag and Drop
- Select the index.zip folder, we just created.
- Save and Depoly.
- Open the link.
- Following page will open.
 
  ![Screenshot 2025-05-12 210138](https://github.com/user-attachments/assets/2f897e37-75af-4721-bc6b-1518bfc891d0)

  ## **step 3: Creating a Lambda function to implement our math functionaliy **
- Go to AWS Lambda service.
- click on "create a function".
- Select "Auther from scratch".
- function name - PowerOfMath.
- Runtime - python 3.9
- click on "create function"
- Copy the following code and paste it on code source.
```
# import the JSON utility package
import json
# import the Python math library
import math

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

    # return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```
- Deploy the code.
- click on "Test the code"
- Select "Create new test event"
    -    Event Name : PowerOfMath
    -    Event Sharing setting: private
    -    Event in JSON:
```
{
   "base" : 2,
   "exponent" : 4
}
```
- You will get status code: 200 , it means our lambda funtion is workig properly.
![Screenshot 2025-05-12 211106](https://github.com/user-attachments/assets/50c37f55-ec69-4ee8-b877-105a03883a42)

## **step 4: Create a API Gateway to Invoke the lambda function**
- Go to AWS API Gateway service.
- Click on "create API".
-Choose "REST API" ,click on "Build".
- Configure the following information.
- API DEtails : New API 
- API Name : PowerOfMathAPI
- API Endpoint : Regional
- Click on "create ApI".
- Now click on "create method.
     -    Method type : POST
     -    Integration type : Lambda function
     -    Lambda funtion : select our lambda funtion "PoweerOfMath"
- Click on create method.
- click on "Enable CORS".
   -    Access-Control-Allow-Methods : POST
- Click on "Save".
- Now click on "Deploy API"
   -    Stage : New Stage
   -    Stage Name : dev
- Click on Deploy and cpoy the API for future use.
- go to resources, click on POST
- test the API with following code.
```
{ 
  "base" : 2,
  "exponent" : 4
}
```
![Screenshot 2025-05-12 212900](https://github.com/user-attachments/assets/1f57468b-e80f-450e-9d6d-12b84b1670c8)

## **step 5: Create DynamoDB database to store the data**
- Go to AWS DynamoDB
- Click on "create table"
- Configure the following information :
     -   Table Name : PowerOfMath
     -   Partition Key : ID
- Create Table
- Click on table and copy the ARN for future use.

## **step 6: Modify the permission of lambda function**
- Go to lambda funtion, we created.
- go to configuration.
- go to permission.
- click on Role name link (now we are in IAM Service).
- Click on "add permissions" :
    -   select "create inline policy"
    -   choose JSON
- Past the following role poicy code.
```
{
"Version": "2012-10-17",
"Statement": [
    {
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "dynamodb:PutItem",
            "dynamodb:DeleteItem",
            "dynamodb:GetItem",
            "dynamodb:Scan",
            "dynamodb:Query",
            "dynamodb:UpdateItem"
        ],
        "Resource": "YOUR-TABLE-ARN"
    }
    ]
}
```
**Note : Replace the   <"Resource" : "YOUR-TABLE-ARN" >  with your actual  DynamoDB Table ARN.**
- click on next and create policy with the name : PowerOfMathDynamoDbPolicy

  ## **step 7: Update the lambda function**
  - Comeback to our lambda function.
  - Replace the code with the following code.
 ```
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
table = dynamodb.Table('PowerOfMathDatabase')
# store the current time in a human readable format in a variable
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

# write result and time to the DynamoDB table using the object we instantiated and save response in a variable
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime':now
            })

# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```
- Deploy and Test the code.
- Go to dynamoDB table.
- Click on "Explore table item" and see if the result of lambda funtion is stored or not.
  ![Screenshot 2025-05-12 215548](https://github.com/user-attachments/assets/58af362d-1c01-4f28-a4cc-10d768489a5d)

 ## **step 8: Invoking API Gateway Endpoint**
 - Open the index.html file we created in first step.
 - Replace the code with following code.
 ```
< !DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>To the Power of Math!</title>
    <!-- Styling for the client UI -->
    <style>
    h1 {
        color: #FFFFFF;
        font-family: system-ui;
		margin-left: 20px;
        }
	body {
        background-color: #222629;
        }
    label {
        color: #86C232;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 20px;
		margin-top: 20px;
        }
     button {
        background-color: #86C232;
		border-color: #86C232;
		color: #FFFFFF;
        font-family: system-ui;
        font-size: 20px;
		font-weight: bold;
        margin-left: 30px;
		margin-top: 20px;
		width: 140px;
        }
	 input {
        color: #222629;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 10px;
		margin-top: 20px;
		width: 100px;
        }
    </style>
    <script>
        // callAPI function that takes the base and exponent numbers as parameters
        var callAPI = (base,exponent)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"base":base,"exponent":exponent});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("https://93qcoxvcp7.execute-api.ap-southeast-1.amazonaws.com/dev", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <h1>TO THE POWER OF MATH!</h1>
	<form>
        <label>Base number:</label>
        <input type="text" id="base">
        <label>...to the power of:</label>
        <input type="text" id="exponent">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button>
    </form>
</body>
</html>
```
**Make sure that you replace the API Gateway Endpoint eith your actual Endpoint.**
![Screenshot 2025-05-12 222506](https://github.com/user-attachments/assets/7b24e14d-6d41-40bc-a922-7ae08a94e6fb)
- Save the updated index.html file and compress it to zip folder.
- Now go back to the AWS Amplify.
- go to our "PowerOfMath" ewb page and click on "Deploy updates"
![Screenshot 2025-05-12 222726](https://github.com/user-attachments/assets/97059ef9-d559-4de2-bea2-9bc452d425ea)
- Drag and Drop : Select the index.zip file we just updated.
- save and deploy.
- Visit Deployment URL.
- Following page will open.
![Screenshot 2025-05-12 221847](https://github.com/user-attachments/assets/8f923b6b-7d60-4d0c-9378-bfa69e63a45c)



  






  
    
