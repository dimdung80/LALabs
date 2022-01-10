Introduction
In this AWS hands-on lab, we will create a fully working serverless reminder application using S3, Lambda, API Gateway, Step Functions, Simple Email Service, and Simple Notification Service.

While the lab does use Python and JavaScript, you don't need to be able to code to understand and implement the solution. By the end of the lab, you will feel more comfortable architecting and implementing serverless solutions within AWS.

Let's get started!

Solution
Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (us-east-1) region throughout the lab.

All of the resources needed to complete this lab are available from this GitHub repo.

Create the email Lambda Function
In the AWS Management Console, navigate to Lambda.
Click Create function.
With Author from scratch selected, set the following values:
Function name: email
Runtime: Python 3.8
Click Change default execution role.
Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
Click Create function.
Scroll down to Code source and double-click lambda_function.py to display the function code.
Delete the provided code.
In a new browser tab, open the GitHub repo for this lab.
Click to open the email_reminder.py file.
Click Raw above the code to display the raw function code.
Copy the code.
Return to the AWS Lambda console, and paste the copied code in to lambda_function.py. Keep this tab open for later.
Verify an Email Address in Simple Email Service (SES)
Open a new browser tab, and navigate to Simple Email Service.
Click Create identity.
Under Identity details, select Email address.
Enter your email address, and click Create identity.
In a new browser tab or email client, navigate to your email, open the SES verification email, and click the provided link.
Go back to Lambda.
Finish the email Lambda Function Setup
Within the Lambda function, delete the YOUR_SES_VERIFIED_EMAIL placeholder, and type in the following replacing YOUR_EMAIL with your verified email address:

VERIFIED_EMAIL = 'YOUR_EMAIL'
Click Deploy.

In Function Overview at the top, copy the function ARN and paste it into a text file for later use.
Create the sms Lambda Function
In the navigation at the top of the page, click Functions to return to the main Lambda Functions page.
Click Create function.
With Author from scratch selected, set the following values:
Function name: sms
Runtime: Python 3.8
Click Change default execution role.
Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
Click Create function.
Scroll down to Code source, and double-click lambda_function.py to display the function code.
Delete the provided code.
In a new browser tab, open the GitHub repo for this lab.
Click the sms_reminder.py file.
Click Raw to display the raw function code.
Copy the code.
Return to the AWS Lambda console, and paste the copied code in tolambda_function.py.
Click Deploy.
In Function Overview at the top, copy the function ARN and paste it into a text file for later use.
Create the api_handler Lambda Function
In the navigation at the top of the page, click Functions to return to the main Lambda Functions page.
Click Create function.
With Author from scratch selected, set the following values:
Function name: api_handler
Runtime: Python 3.8
Click Change default execution role.
Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
Click Create function.
Scroll down to Code source, and double-click lambda_function.py to display the function code.
Delete the provided code.
In a new browser tab, open the GitHub repo for this lab.
Click the api_handler.py file.
Click Raw to display the raw function code.
Copy the code.
Return to the AWS Lambda console, and paste the copied code in to lambda_function.py.
Click Deploy. Keep this tab open.
Create a Step Function State Machine
In a new browser tab, navigate to the Step Functions.
Click Get started to run the Hello World example.
In the line under the Review Hello World example title, click here to access more functionality. (Click Leave if prompted with a warning, and then click here again.)
Select Write your workflow in code.
Under Type, select Standard.
Return to the GitHub repo, and click the step-function-template.json file.
Click Raw, and copy the code.
Return to Step Functions, and under Definition, delete the current contents and replace it with your copied code.
On lines 34 and 52, replace the Resource placeholder value with the copied ARN for email.
On lines 40 and 62, replace the Resource placeholder value with the copied ARN for sms.
Click the refresh icon to the right of the code to view the updated function diagram.
Click Next.
Under Permissions, select Choose an existing role.
Under Existing roles, select RoleForStepFunction.
Scroll to the bottom, and click Create state machine.
Under Details, copy the ARN.
Return to Lambda, and select Functions > api_handler.
Scroll down to Code source and double-click lambda_function.py.
On line 6, replace the SFN_ARN placeholder value with the ARN you just copied.
Click Deploy.
Create the API Gateway
In the AWS Management Console, navigate to API Gateway.
Click Get Started, and then click OK.
Under REST API (not the Private one), select Build.
Under Choose the protocol, select REST.
Under Create new API, select New API.
Under Settings, set the following values:
API name: reminders
Endpoint Type: Regional
Click Create API.
Click Actions > Create Resource.
In Resource Name, enter reminders.
Select Enable API Gateway CORS.
Click Create Resource.
With /reminders selected, click Actions > Create Method.
In the new dropdown under /reminders, select POST and click the adjacent checkmark icon.
In /reminders - POST - Setup, set the following values:
Integration type: Lambda Function
Use Lambda Proxy integration: Selected
Lambda Region: us-east-1
Lambda Function: api_handler
Click Save.
Click OK.
Click Actions > Deploy API.
Set the following values:
Deployment stage: New Stage
Stage name: prod
Click Deploy.
Note: You may ignore any Web Application Firewall (WAF) permissions warning messages received after deployment.

At the top of the page, under prod Stage Editor, copy the Invoke URL for later use.
Create the Static S3 Website
Return to the GitHub repo, and download a local copy of the static_website folder with its file contents. You can go to the static_website folder and download a copy of each file, or you can go to the main repository, click Code, and select Download ZIP to get all of the files in the repo.
In your local static_website folder, open the formlogic.js file.
On line 5, delete the API_ENDPOINT placeholder text, and paste in the Invoke URL you previously copied from API Gateway, keeping /reminders on the end of the string:

var API_ENDPOINT = 'UPDATETOYOURINVOKEURLENDPOINT/reminders';
In the AWS Management Console, navigate to S3.

Click Create bucket.
In Bucket name, enter a globally unique bucket name.
Under Object Ownership, select ACLs enabled.
Uncheck Block all public access.
Select I acknowledge that the current settings might result in this bucket and the objects within becoming public.
Scroll down, and click Create bucket.
Select the new bucket to open it.
Click Upload > Add files.
Find your local copy of the static_website folder, select all the files and click Open.
Scroll down, and click Permissions to expand access options.
Under Predefined ACLs, select Grant public-read access.
Select I understand the risk of granting public-read access to the specified objects.
Click Upload.
Once uploaded, click Close.
Click the Properties tab.
Scroll down to Static website hosting, and click Edit.
Select Enable.
Set the following values:
Index document: index.html
Error document: error.html
Click Save changes.
Scroll down again to Static website hosting, and click the Bucket website endpoint URL to access the webpage.
Testing the Static S3 Website
To test the service's functionality, set the following values:
Seconds to wait: 1
Message: Hello
someone@something.com: Your email address
Under Reminder Type, select email. You should see {"Status":"Success"} at the bottom of the page.
Check your email. You should now see a reminder email from the service.
Note: Check your spam folder if you do not see it in your inbox.

Navigate to AWS Step Functions.
Click MyStateMachine to open it.
Under Executions, click on the link for the event that just occurred, and scroll down to Graph inspector to view the event's visual workflow.
Conclusion
Congratulations — you've completed this hands-on lab!
