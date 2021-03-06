# Example: Create a Trigger in AWS CodeCommit for an Existing AWS Lambda Function<a name="how-to-notify-lambda-cc"></a>

The easiest way to create a trigger that will invoke a Lambda function is to create that trigger in the Lambda console\. This built\-in integration ensures that AWS CodeCommit will have the permissions required to run the function\. You can add a trigger for an existing Lambda function by going to the Lambda console, choosing the function, and on the **Triggers** tab for the function, and then following the steps in **Add trigger**\. These are similar steps to the ones shown in [Create the Lambda Function](how-to-notify-lambda.md#how-to-notify-lambda-create-function)\.

However, you can also create a trigger for a Lambda function in an AWS CodeCommit repository\. Doing so requires that you choose an existing Lambda function to invoke, and also requires that you manually configure the permissions required for AWS CodeCommit to run the function\.

**Topics**
+ [Manually Configure Permissions to Allow AWS CodeCommit to Run a Lambda Function](#how-to-notify-lam-perm)
+ [Create a Trigger for the Lambda Function in an AWS CodeCommit Repository \(Console\)](#how-to-notify-lam-console)
+ [Create a Trigger to a Lambda Function for an AWS CodeCommit Repository \(AWS CLI\)](#how-to-notify-lam-cli)

## Manually Configure Permissions to Allow AWS CodeCommit to Run a Lambda Function<a name="how-to-notify-lam-perm"></a>

If you create a trigger in AWS CodeCommit that invokes a Lambda function, you must manually configure the permissions to allow AWS CodeCommit to run the Lambda function\. To avoid this manual configuration, consider creating the trigger in the Lambda console for the function instead\.<a name="how-to-notify-lambda-create-function-perm"></a>

**To allow AWS CodeCommit to run a Lambda function**

1. Open a plain\-text editor and create a JSON file that specifies the Lambda function name, the details of the AWS CodeCommit repository, and the actions you want to allow in Lambda, similar to the following:

   ```
   {
       "FunctionName": "MyCodeCommitFunction", 
       "StatementId": "1", 
       "Action": "lambda:InvokeFunction", 
       "Principal": "codecommit.amazonaws.com", 
       "SourceArn": "arn:aws:codecommit:us-east-1:80398EXAMPLE:MyDemoRepo", 
       "SourceAccount": "80398EXAMPLE"
   }
   ```

1. Save the file as a JSON file with a name that is easy for you to remember \(for example, *AllowAccessfromMyDemoRepo*\.json\)\.

1. At the terminal \(Linux, macOS, or Unix\) or command line \(Windows\), run the aws lambda add\-permissions command to add a permission to the resource policy associated with your Lambda function, using the JSON file you just created:

   ```
   aws lambda add-permission - -cli-input-json file://AllowAccessfromMyDemoRepo.json
   ```

   This command returns the JSON of the policy statement you just added, similar to the following:

   ```
   {
       "Statement": "{\"Condition\":{\"StringEquals\":{\"AWS:SourceAccount\":\"80398EXAMPLE\"},\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:codecommit:us-east-1:80398EXAMPLE:MyDemoRepo\"}},\"Action\":[\"lambda:InvokeFunction\"],\"Resource\":\"arn:aws:lambda:us-east-1:80398EXAMPLE:function:MyCodeCommitFunction\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"codecommit.amazonaws.com\"},\"Sid\":\"1\"}"
   }
   ```

   For more information about resource policies for Lambda functions, see [AddPermission](http://docs.aws.amazon.com/lambda/latest/dg/API_AddPermission.html) and [The Pull/Push Event Models](http://docs.aws.amazon.com/lambda/latest/dg/intro-invocation-modes.html) in the Lambda User Guide\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the **Dashboard** navigation pane, choose **Roles**, and in the list of roles, select *lambda\_basic\_execution*\.

1. On the summary page for the role, choose the **Permissions** tab, and in the **Inline Policies** section, choose **Create Role Policy**\.

1. On the **Set Permissions** page, choose **Policy Generator**, and then choose **Select**\.

1. On the **Edit Permissions** page, do the following:
   + In **Effect**, choose **Allow**\.
   + In **AWS Service**, choose **AWS CodeCommit**\.
   + In **Actions**, select **GetRepository**\.
   + In **Amazon Resource Name \(ARN\)**, type the ARN for the repository \(for example, `arn:aws:codecommit:us-east-1:80398EXAMPLE:MyDemoRepo`\)\.

   Choose **Add Statement**, and then choose **Next Step**\.

1. On the **Review Policy** page, choose **Apply Policy**\. 

   Your policy statement should look similar to the following example:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "Stmt11111111",
               "Effect": "Allow",
               "Action": [
                   "codecommit:GetRepository"
               ],
               "Resource": [
                   "arn:aws:codecommit:us-east-1:80398EXAMPLE:MyDemoRepo"
               ]
           }
       ]
   }
   ```

## Create a Trigger for the Lambda Function in an AWS CodeCommit Repository \(Console\)<a name="how-to-notify-lam-console"></a>

After you have created the Lambda function, you can create a trigger in AWS CodeCommit that will run the function in response to the repository events you specify\.

**Note**  
Before you can successfully test or run the trigger for the example, you must configure the policies that allow AWS CodeCommit to invoke the function and the Lambda function to get information about the repository\. For more information, see [To allow AWS CodeCommit to run a Lambda function](#how-to-notify-lambda-create-function-perm)\.

**To create a trigger for a Lambda function in the AWS CodeCommit console**

1. Open the AWS CodeCommit console at [https://console\.aws\.amazon\.com/codecommit](https://console.aws.amazon.com/codecommit)\.

1. From the list of repositories, choose the repository where you want to create triggers for repository events\.

1. In the navigation pane for the repository, choose **Settings**\. In **Settings**, choose **Triggers**\.

1. Choose **Create trigger**\.

1. In the **Create trigger** pane, do the following:
   + In **Trigger name**, type a name for the trigger \(for example, *MyLambdaFunctionTrigger*\)\.
   + In **Events**, choose the repository events that will trigger the Lambda function\. 

     If you choose **All repository events**, you cannot choose any other events\. If you want to choose a subset of events, clear **All repository events**, and then choose the events you want from the list\. For example, if you want the trigger to run only when a user creates a tag or a branch in the AWS CodeCommit repository, remove **All repository events**, and then choose **Create branch or tag**\.
   + If you want the trigger to apply to all branches of the repository, in **Branches**, choose **All branches**\. Otherwise, choose **Specific branches**\. The default branch for the repository will be added by default\. You can keep or delete this branch from the list\. Choose up to ten branch names from the list of repository branches\.
   + In **Send to**, choose **AWS Lambda**\.
   + In **Lambda function ARN**, choose the function name from the list, or choose **Add an AWS Lambda function ARN** and then type the ARN for the function\.
   + In **Custom data**, optionally provide information you want included in the Lambda function \(for example, the name of the IRC channel used by developers to discuss development in the repository\)\. This field is a string\. It cannot be used to pass any dynamic parameters\.

1. Optionally, choose **Test trigger**\. This option will attempt to invoke the function with sample data about your repository, including the most recent commit ID for the repository\. \(If no commit history exists, sample values consisting of zeroes will be generated instead\.\) This will help you confirm you have correctly configured access between AWS CodeCommit and the Lambda function\.

1. Choose **Create** to finish creating the trigger\.

1. To verify the functionality of the trigger, make and push a commit to the repository where you configured the trigger\. You should see a response from the Lambda function on the **Monitoring** tab for that function in the Lambda console\. 

## Create a Trigger to a Lambda Function for an AWS CodeCommit Repository \(AWS CLI\)<a name="how-to-notify-lam-cli"></a>

You can also use the command line to create a trigger for a Lambda function in response to AWS CodeCommit repository events, such as when someone pushes a commit to your repository\. 

**To create a trigger for an Lambda function**

1. Open a plain\-text editor and create a JSON file that specifies:
   + The Lambda function name\.
   + The repository and branches you want to monitor with this trigger\. \(If you do not specify any branches, the trigger will apply to all branches in the repository\.\)
   + The events that will activate this trigger\.

    Save the file\. 

   For example, if you want to create a trigger for a repository named *MyDemoRepo* that will publish all repository events to a Lambda function named *MyCodeCommitFunction* for two branches, *master* and *preprod*:

   ```
   {
       "repositoryName": "MyDemoRepo",
       "triggers": [
           {
               "name": "MyLambdaFunctionTrigger",
               "destinationArn": "arn:aws:lambda:us-east-1:80398EXAMPLE:function:MyCodeCommitFunction",
               "customData": "",
               "branches": [
                   "master", "preprod"
               ],
               "events": [
                   "all"
               ]
           }
       ]
   }
   ```

   There must be a trigger block in the JSON for each trigger for a repository\. To create more than one trigger for a repository, include additional blocks in the JSON\. Remember that all triggers created in this file are for the specified repository\. You cannot create triggers for multiple repositories in a single JSON file\. For example, if you wanted to create two triggers for a repository, you could create a JSON file with two trigger blocks\. In the following example, no branches are specified in the second trigger block, so that trigger will apply to all branches:

   ```
   {
       "repositoryName": "MyDemoRepo",
       "triggers": [
           {
               "name": "MyLambdaFunctionTrigger",
               "destinationArn": "arn:aws:lambda:us-east-1:80398EXAMPLE:function:MyCodeCommitFunction",
               "customData": "",
               "branches": [
                   "master", "preprod"
               ],
               "events": [
                   "all"
               ]
           },
           {
               "name": "MyOtherLambdaFunctionTrigger",
               "destinationArn": "arn:aws:lambda:us-east-1:80398EXAMPLE:function:MyOtherCodeCommitFunction",
               "customData": "",
               "branches": [],
               "events": [
                   "updateReference", "deleteReference"
               ]
           }
       ]
   }
   ```

   You can create triggers for events you specify, such as when a commit is pushed to a repository\. Event types include:
   + `all` for all events in the specified repository and branches\.
   + `updateReference` for when commits are pushed to the specified repository and branches\.
   + `createReference` for when a new branch or tag is created in the specified repository\.
   + `deleteReference` for when a branch or tag is deleted in the specified repository\.
**Note**  
You can use more than one event type in a trigger\. However, if you specify `all`, you cannot specify other events\. 

   To see the full list of valid event types, at the terminal or command prompt, type aws codecommit put\-repository\-triggers help\. 

   In addition, you can include a string in `customData` \(for example, an IRC channel name developers use when discussing development in this repository\)\. This field is a string\. It cannot be used to pass any dynamic parameters\. This string will be appended as an attribute to the AWS CodeCommit JSON returned in response to the trigger\.

1. At a terminal or command prompt, optionally run the test\-repository\-triggers command\. For example, the following is used to test that the JSON file named *trigger\.json* is valid and that AWS CodeCommit can trigger the Lambda function\. This test uses sample data to trigger the function if no real data is available\.

   ```
   aws codecommit test-repository-triggers --cli-input-json file://trigger.json
   ```

   If successful, this command returns information similar to the following:

   ```
   {
       "successfulExecutions": [
           "MyLambdaFunctionTrigger"
       ],
       "failedExecutions": []
   }
   ```

1. At a terminal or command prompt, run the put\-repository\-triggers command to create the trigger in AWS CodeCommit\. For example, to use a JSON file named *trigger\.json* to create the trigger:

   **aws codecommit put\-repository\-triggers \- \-cli\-input\-json file://*trigger\.json***

   This command returns a configuration ID, similar to the following:

   ```
   {
       "configurationId": "0123456-I-AM-AN-EXAMPLE"
   }
   ```

1. To view the configuration of the trigger, run the get\-repository\-triggers command, specifying the name of the repository:

   **aws codecommit get\-repository\-triggers \- \-repository\-name *MyDemoRepo***

   This command returns the structure of all triggers configured for the repository, similar to the following:

   ```
   {
       "configurationId": "0123456-I-AM-AN-EXAMPLE",
       "triggers": [
           {
               "events": [
                   "all"
               ],
               "destinationArn": "arn:aws:lambda:us-east-1:80398EXAMPLE:MyCodeCommitFunction",
               "branches": [
                   "master",
                   "preprod"
               ],
               "name": "MyLambdaFunctionTrigger",
               "customData": "Project ID 12345"
           }
       ]
   }
   ```

1. To test the functionality of the trigger, make and push a commit to the repository where you configured the trigger\. You should see a response from the Lambda function on the **Monitoring** tab for that function in the Lambda console\. 