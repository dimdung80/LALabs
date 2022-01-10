# Building a Serverless Application Using Step Functions, API Gateway, Lambda, and S3 in AWS
## Introduction
In this AWS hands-on lab, we will create a fully working serverless reminder application using S3, Lambda, API Gateway, Step Functions, Simple Email Service, and Simple Notification Service.

While the lab does use Python and JavaScript, you don't need to be able to code to understand and implement the solution. By the end of the lab, you will feel more comfortable architecting and implementing serverless solutions within AWS.

Let's get started!

## Solution
Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (us-east-1) region throughout the lab.

All of the resources needed to complete this lab are available from this GitHub repo.

### Create the email Lambda Function
1. In the AWS Management Console, navigate to Lambda.
2. Click Create function.
3. With Author from scratch selected, set the following values:
   - Function name: email
   - Runtime: Python 3.8
4. Click Change default execution role.
5. Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
6. Click Create function.
7. Scroll down to Code source and double-click lambda_function.py to display the function code.
8. Delete the provided code.
9. In a new browser tab, open the GitHub repo for this lab.
10. Click to open the email_reminder.py file.
11. Click Raw above the code to display the raw function code.
12. Copy the code.
13. Return to the AWS Lambda console, and paste the copied code in to lambda_function.py. Keep this tab open for later.

### Verify an Email Address in Simple Email Service (SES)
1. Open a new browser tab, and navigate to Simple Email Service.
2. Click Create identity.
3. Under Identity details, select Email address.
4. Enter your email address, and click Create identity.
5. In a new browser tab or email client, navigate to your email, open the SES verification email, and click the provided link.
6. Go back to Lambda.

### Finish the email Lambda Function Setup
1. Within the Lambda function, delete the YOUR_SES_VERIFIED_EMAIL placeholder, and type in the following replacing YOUR_EMAIL with your verified email address:

    - VERIFIED_EMAIL = 'YOUR_EMAIL'
2. Click Deploy.

3. In Function Overview at the top, copy the function ARN and paste it into a text file for later use.

### Create the sms Lambda Function
1. In the navigation at the top of the page, click Functions to return to the main Lambda Functions page.
2. Click Create function.
3. With Author from scratch selected, set the following values:
   - Function name: sms
   - Runtime: Python 3.8
4. Click Change default execution role.
5. Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
6. Click Create function.
7. Scroll down to Code source, and double-click lambda_function.py to display the function code.
8. Delete the provided code.
9. In a new browser tab, open the GitHub repo for this lab.
10. Click the sms_reminder.py file.
11. Click Raw to display the raw function code.
12. Copy the code.
13. Return to the AWS Lambda console, and paste the copied code in tolambda_function.py.
14. Click Deploy.
15. In Function Overview at the top, copy the function ARN and paste it into a text file for later use.

### Create the api_handler Lambda Function
1. In the navigation at the top of the page, click Functions to return to the main Lambda Functions page.
2. Click Create function.
3. With Author from scratch selected, set the following values:
   - Function name: api_handler
   - Runtime: Python 3.8
4. Click Change default execution role.
5. Select Use an existing role, and pick the LambdaRuntimeRole from the dropdown.
6. Click Create function.
7. Scroll down to Code source, and double-click lambda_function.py to display the function code.
8. Delete the provided code.
9. In a new browser tab, open the GitHub repo for this lab.
10. Click the api_handler.py file.
11. Click Raw to display the raw function code.
12. Copy the code.
13. Return to the AWS Lambda console, and paste the copied code in to lambda_function.py.
14. Click Deploy. Keep this tab open.

### Create a Step Function State Machine
1. In a new browser tab, navigate to the Step Functions.
2. Click Get started to run the Hello World example.
3. In the line under the Review Hello World example title, click here to access more functionality. (Click Leave if prompted with a warning, and then click here again.)
4. Select Write your workflow in code.
5. Under Type, select Standard.
6. Return to the GitHub repo, and click the step-function-template.json file.
7. Click Raw, and copy the code.
8. Return to Step Functions, and under Definition, delete the current contents and replace it with your copied code.
9. On lines 34 and 52, replace the Resource placeholder value with the copied ARN for email.
10. On lines 40 and 62, replace the Resource placeholder value with the copied ARN for sms.
11. Click the refresh icon to the right of the code to view the updated function diagram.
12. Click Next.
13. Under Permissions, select Choose an existing role.
14. Under Existing roles, select RoleForStepFunction.
15. Scroll to the bottom, and click Create state machine.
16. Under Details, copy the ARN.
17. Return to Lambda, and select Functions > api_handler.
18. Scroll down to Code source and double-click lambda_function.py.
19. On line 6, replace the SFN_ARN placeholder value with the ARN you just copied.
20. Click Deploy.

### Create the API Gateway
1. In the AWS Management Console, navigate to API Gateway.
2. Click Get Started, and then click OK.
3. Under REST API (not the Private one), select Build.
4. Under Choose the protocol, select REST.
5. Under Create new API, select New API.
6. Under Settings, set the following values:
   - API name: reminders
   - Endpoint Type: Regional
7. Click Create API.
8. Click Actions > Create Resource.
9. In Resource Name, enter reminders.
10. Select Enable API Gateway CORS.
11. Click Create Resource.
12 With /reminders selected, click Actions > Create Method.
13. In the new dropdown under /reminders, select POST and click the adjacent checkmark icon.
14. In /reminders - POST - Setup, set the following values:
    - Integration type: Lambda Function
    - Use Lambda Proxy integration: Selected
    - Lambda Region: us-east-1
    - Lambda Function: api_handler
15. Click Save
16. Click OK
17. Click Actions > Deploy API.
18. Set the following values:
    - Deployment stage: New Stage
    - Stage name: prod
19. Click Deploy
Note: You may ignore any Web Application Firewall (WAF) permissions warning messages received after deployment.

20. At the top of the page, under prod Stage Editor, copy the Invoke URL for later use.

### Create the Static S3 Website
1. Return to the GitHub repo, and download a local copy of the static_website folder with its file contents. You can go to the static_website folder and download a copy of each file, or you can go to the main repository, click Code, and select Download ZIP to get all of the files in the repo.
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
