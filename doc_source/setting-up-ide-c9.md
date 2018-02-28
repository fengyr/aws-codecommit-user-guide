# Integrate AWS Cloud9 with AWS CodeCommit<a name="setting-up-ide-c9"></a>

You can use AWS Cloud9 to make code changes in an AWS CodeCommit repository\. AWS Cloud9 contains a collection of tools that you can use to write code, as well as build, run, test, debug, and release software\. You can clone existing repositories, create repositories, commit and push code changes to a repository, and more, all from your AWS Cloud9 EC2 development environment\. The AWS Cloud9 EC2 development environment is generally preconfigured with the AWS CLI, an Amazon EC2 role, and Git, so in most cases, you can run a few simple commands and start interacting with your repository\.

To use AWS Cloud9 with AWS CodeCommit, you need the following:

+ An AWS Cloud9 EC2 development environment running on Amazon Linux\.

+ The AWS Cloud9 IDE open in a web browser\.

+ An IAM user with one of the AWS CodeCommit managed policies and one of the AWS Cloud9 managed policies applied to it\.

  For more information, see [AWS Managed \(Predefined\) Policies for AWS CodeCommit](auth-and-access-control-iam-identity-based-access-control.md#managed-policies) and [Understanding and Getting Your Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html)\.


+ [Step 1: Create an AWS Cloud9 Development Environment](#setting-up-ide-c9-connect)
+ [Step 2: Configure the AWS CLI Credential Helper On Your AWS Cloud9 EC2 Development Environment](#setting-up-ide-c9-credentials)
+ [Step 3: Clone an AWS CodeCommit Repository Into Your AWS Cloud9 EC2 Development Environment](#setting-up-ide-c9-checkout)
+ [Next Steps](#setting-up-ide-c9-next)

## Step 1: Create an AWS Cloud9 Development Environment<a name="setting-up-ide-c9-connect"></a>

AWS Cloud9 will host your development environment on an Amazon EC2 instance\. This is the easiest way to integrate, as you can use the AWS managed temporary credentials for the instance to connect to your AWS CodeCommit repository\. If you want to use your own server instead, see the [AWS Cloud9 User Guide](http://docs.aws.amazon.com/cloud9/latest/user-guide/)\. 

**To create an AWS Cloud9 environment**

1. Sign in to AWS as the IAM user you've configured and open the AWS Cloud9 console\.

1. In the AWS Cloud9 console, choose **Create environment**\.

1. In **Step 1: Name environment**, type a name for your development environment in **Name**\. Optionally add a description for the environment, and then choose **Next step**\.

1. In **Step 2: Configure Settings**, configure your environment as follows:

   + In **Environment type**, choose **Create a new instance for environment \(EC2\)**\.

   + In **Instance type**, choose the appropriate instance type for your development environment\. For example, if you're just exploring the service, you might choose the default of t2\.micro\. If you intend to use this environment for development work, choose a larger instance type\.

   + Accept the other default settings unless you have specific reasons to choose otherwise \(for example, your organization uses a specific VPC, or your AWS account does not have any VPCs configured\), and then choose **Next step**\.

1. In **Step 3: Review**, review your settings\. Choose **Previous step** if you want to make any changes\. If not, choose **Create environment**\.

   Creating an environment and connecting to it for the first time takes several minutes\. If it seems to take an unusally long time, see [Troubleshooting](http://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html) in the AWS Cloud9 User Guide\.

1. Once you are connected to your environment, check to see if Git is already installed and is a supported version by running the git \-\-version command in the terminal window\.  
![\[Verifying that Git is installed and that it is a supported version in the AWS Cloud9 development environment.\]](http://docs.aws.amazon.com/codecommit/latest/userguide/images/codecommit-c9-git.png)

   If Git is not installed, or if it is not a supported version, install a supported version\. AWS CodeCommit supports Git versions 1\.7\.9 and later\. To install Git, we recommend websites such as [Git Downloads](http://git-scm.com/downloads)\. 
**Tip**  
Depending on the operating system of your environment, you might be able to use the yum command with the sudo option to install updates, including Git\. For example, an administrative command sequence might resemble the following three commands:  

   ```
   sudo yum -y update
   sudo yum -y install git
   git --version
   ```

1. Configure a user name and email to be associated with your Git commits by running the** git config** command\. For example:

   ```
       git config --global user.name "Mary Major"
       git config --global user.email mary.major@example.com
   ```

## Step 2: Configure the AWS CLI Credential Helper On Your AWS Cloud9 EC2 Development Environment<a name="setting-up-ide-c9-credentials"></a>

After you've created an AWS Cloud9 environment, you can configure the AWS CLI Credential Helper to manage the credentials for connections to your AWS CodeCommit repository\. The AWS Cloud9 development environment comes with AWS managed temporary credentials that are associated with your IAM user\. You will use these credentials with the AWS CLI credential helper\.

1. Open the terminal window and type the following command to verify that the AWS CLI is installed:

   ```
   aws --version
   ```

   If successful, this command returns the currently\-installed version of the AWS CLI\. To upgrade an older version of the AWS CLI to the latest version, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

1. At the terminal, type the following commands to configure the AWS CLI Credential Helper for HTTPS connections:

   ```
   git config --global credential.helper '!aws codecommit credential-helper $@'
   git config --global credential.UseHttpPath true
   ```
**Tip**  
The credential helper will use the default Amazon EC2 instance role for your development environment\. If you intend to use the development environment to connect to repositories that are not hosted in AWS CodeCommit, either configure SSH connections to those repositories, or configure a local `.gitconfig` file to use an alternative credential management system when connecting to those other repositories\. For more information, see [Git Tools \- Credential Storage](https://git-scm.com/book/en/v2/Git-Tools-Credential-Storage) on the Git website\.

## Step 3: Clone an AWS CodeCommit Repository Into Your AWS Cloud9 EC2 Development Environment<a name="setting-up-ide-c9-checkout"></a>

Once you've configured the AWS CLI credential helper, you can clone your AWS CodeCommit repository onto it\. Then you can start working with the code\.

1. In the terminal, run the git clone command, specifying the HTTPS clone URL of the repository you want to clone\. For example, if you want to clone a repository named MyDemoRepo in the US East \(Ohio\) region, you would type:

   ```
   git clone https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo
   ```
**Tip**  
You can find the Clone URL for your repository in the AWS CodeCommit console, both on the **Dashboard**, and in the **Connect** information on the **Code** page of the repository itself\.  
![\[The HTTPS clone URL of a repository, as viewed and copied from the AWS CodeCommit console dashboard.\]](http://docs.aws.amazon.com/codecommit/latest/userguide/images/codecommit-dashboard-url.png)

1. When the cloning is complete, expand the folder for your repository in the side navigation, and choose the file you want to open for editing\. Alternatively, choose **File** and then choose **New File** to create a new file\.

1. When you have finished editing or creating files, in the terminal window, change directories to your cloned repository and then commit and push your changes\. For example, if you added a new file named *MyFile\.py*:

   ```
   cd MyDemoRepo
   git commit -a MyFile.py
   git commit -m "Added a new file with some code improvements"
   git push
   ```

## Next Steps<a name="setting-up-ide-c9-next"></a>

To learn more about using AWS Cloud9, see the [AWS Cloud9 User Guide](http://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html)\. To learn more about using Git with AWS CodeCommit, see [Git with AWS CodeCommit Tutorial](getting-started.md)\.