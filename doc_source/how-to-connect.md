# Connect to an AWS CodeCommit Repository<a name="how-to-connect"></a>

When you connect to an AWS CodeCommit repository for the first time, you typically clone its contents to your local machine\. Alternatively, if you already have a local repo, you can add an AWS CodeCommit repository as a remote\. This topic provides instructions for connecting to an AWS CodeCommit repository\. If you want to migrate an existing repository to AWS CodeCommit, see [Migrate to AWS CodeCommit](how-to-migrate-repository.md)\.

**Note**  
Depending on your usage, you might be charged for creating or accessing a repository\. For more information, see [Pricing](http://aws.amazon.com/codecommit/pricing) on the AWS CodeCommit product information page\.

**Topics**
+ [Prerequisites for Connecting to an AWS CodeCommit Repository](#how-to-connect-prerequisites)
+ [Connect to the AWS CodeCommit Repository by Cloning the Repository](#how-to-connect-http)
+ [Connect a Local Repo to the AWS CodeCommit Repository](#how-to-connect-local)

## Prerequisites for Connecting to an AWS CodeCommit Repository<a name="how-to-connect-prerequisites"></a>

Before you can connect to an AWS CodeCommit repository:
+ You must have configured your local computer with the software and settings required to connect to AWS CodeCommit\. For more information, see [Setting Up ](setting-up.md)\.
+ You must have the clone URL of the AWS CodeCommit repository to which you want to connect\. This URL includes the name of the repository as well as its AWS region\. For more information, see [View Repository Details](how-to-view-repository-details.md)\.

  If you have not yet created an AWS CodeCommit repository, follow the instructions in [Create a Repository](how-to-create-repository.md), copy the clone URL of the new AWS CodeCommit repository, and return to this page\.

  If you have an AWS CodeCommit repository but you do not know its name, follow the instructions in [View Repository Details](how-to-view-repository-details.md)\.
+ You must have a location on your local machine to store a local copy of the AWS CodeCommit repository to which you will be connecting\. \(This local copy of the AWS CodeCommit repository is known as a *local repo*\.\) You then switch to and run Git commands from that location\. For example, you could use `/tmp` \(for Linux, macOS, or Unix\) or `c:\temp` \(for Windows\)\.
**Note**  
You can use any directory you want\. If you use a different directory than `/tmp` or `c:\temp`, be sure to substitute it for ours when you follow these instructions\.

## Connect to the AWS CodeCommit Repository by Cloning the Repository<a name="how-to-connect-http"></a>

If you do not already have a local repo, follow the steps in this procedure to clone the AWS CodeCommit repository to your local machine\.

1. Complete the prerequisites, including [Setting Up ](setting-up.md)\.
**Important**  
If you have not completed setup, you will not be able to connect to or clone the repository\.

1. From the `/tmp` directory or the `c:\temp` directory, use Git to run the clone command, as shown in the following example for cloning a repository named *MyDemoRepo* in the US East \(Ohio\) region:

   For HTTPS:

   ```
   git clone https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo
   ```

   For SSH:

   ```
   git clone ssh://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo
   ```

   In this example, `git-codecommit.us-east-2.amazonaws.com` is the Git connection point for the US East \(Ohio\) region where the repository exists, `MyDemoRepo` represents the name of your AWS CodeCommit repository, and `my-demo-repo` represents the name of the directory Git will create in the `/tmp` directory or the `c:\temp` directory\. For more information about the regions that support AWS CodeCommit and the Git connections for those regions, see [Regions and Git Connection Endpoints](regions.md)\.
**Note**  
When you use SSH on Windows operating systems to clone a repository, you might need to add the SSH key ID to the connection string as follows:  

   ```
   git clone ssh://Your-SSH-Key-ID@git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo
   ```
For more information, see [For SSH Connections on Windows](setting-up-ssh-windows.md) and [Troubleshooting](troubleshooting.md)\.

   After Git creates the directory, it will pull down a copy of your AWS CodeCommit repository into the newly created directory\.

   If the AWS CodeCommit repository is new or otherwise empty, you will see a message that you are cloning an empty repository\. This is expected\.
**Note**  
If you receive an error that Git can't find the AWS CodeCommit repository or that you don't have permission to connect to the AWS CodeCommit repository, make sure you completed the [prerequisites](setting-up.md), including assigning permissions to the IAM user and setting up your IAM user credentials for Git and AWS CodeCommit on the local machine\. Also, make sure you specified the correct repository name\.

After you successfully connect your local repo to your AWS CodeCommit repository, you are now ready to start running Git commands from the local repo to create commits, branches, and tags and push to and pull from the AWS CodeCommit repository\.

## Connect a Local Repo to the AWS CodeCommit Repository<a name="how-to-connect-local"></a>

Complete the following steps if you already have a local repo and want to add an AWS CodeCommit repository as the remote repository\. If you already have a remote repository and want to push your commits to AWS CodeCommit as well as that other remote repository, follow the steps in [Push Commits to Two Repositories](how-to-mirror-repo-pushes.md) instead\.

1. Complete the [prerequisites](#how-to-connect-prerequisites)\.

1. From the command prompt or terminal, switch to your local repo directory and run the git remote add command to add the AWS CodeCommit repository as a remote repository for your local repo\.

   For example, the following command adds the remote nicknamed **origin** to https://git\-codecommit\.us\-east\-2\.amazonaws\.com/v1/repos/MyDemoRepo:

   For HTTPS:

   ```
   git remote add origin https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo
   ```

   For SSH:

   ```
   git remote add origin ssh://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo
   ```

   This command returns nothing\.

1. To verify you have added the AWS CodeCommit repository as a remote for your local repo, run the git remote \-v command , which should create output similar to the following:

   For HTTPS:

   ```
   origin  https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo (fetch)      
   origin  https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo (push)
   ```

   For SSH:

   ```
   origin  ssh://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo (fetch)       
   origin  ssh://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo (push)
   ```

After you successfully connect your local repo to your AWS CodeCommit repository, you are ready to start running Git commands from the local repo to create commits, branches, and tags, and to push to and pull from the AWS CodeCommit repository\.