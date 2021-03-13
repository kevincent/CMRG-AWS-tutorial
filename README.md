# CMRG-AWS-tutorial
A brief hands on tutorial for CMRG members looking to get familiar with AWS

## Log in to the AWS Console
You will first need to request access to CMRG's AWS account through ITS (see Jen).

Log in using your SSO account at https://awsconsole.ucsd.edu.

## Launch an EC2 Instance
Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud.
 
- select the `EC2` service which should be the top option below `Compute`.  Take note of which aws region you are in.  **US West(Oregon)`us-west-2`*** is a good region to select for this tutorial.  

- Select the orange `Launch instance` button.

![Launch Instance](https://user-images.githubusercontent.com/21269613/110983979-58aeaf00-831f-11eb-8636-6506c6e84c33.png)

### Select AMI
An Amazon Machine Image (AMI) defines the software configuration (operating system, application server, and applications) that your ec2 instance will run.  If you have specific software you want to run, you may install that software on an ec2 instance and save that installation as an AMI to retrieve for later use.  For this tutorial, the default `Amazon Linux 2 AMI` is sufficient.  

- Hit the `Select` button to choose that AMI and continue setting up the ec2 instance.

### Choose your Instance Type
The instance type you select determines the resources you provide your AMI to run with.  The two main features are the number of CPUs and the memory type.  Different computational tasks will have very different needs and require different Instance Types.  The default `t2.micro` is sufficient for this tutorial.

![InstanceType](https://user-images.githubusercontent.com/21269613/110986310-5c900080-8322-11eb-9873-0ffcbd657f07.png | width=100)

- Select the blue `Review and Launch` button on the bottom right of the screen

#### A note on cost
The instance type is also the primary determinant also of the ec2 instance cost (hard disk storage and I/O also have expenses, but they are typically smaller).  AWS publishes the cost of ec2 instances (https://aws.amazon.com/ec2/pricing/on-demand/). Consider using spot instance(s) to reduce cost (https://aws.amazon.com/ec2/spot/pricing/).  Know what instance type you need for your task and don't over provision your intance.  Terminate your instance promptly when you are finished with it to avoid unneeded costs.  Be extra careful if you are spinning up an expensive instance type.

### Review the EC2 instance options and Launch the Instance
The next screen will show all of the options selected for the ec2 instance we are about to launch.  There were a number of options we skipped over such as the amount of hard disk storage to provide the instance and whether or not to use hyperthreading. Look over the options on this page to get a feel for the available options.  Many of the options require AWS knowledge that is beyond the scope of this introductory tutorial.

- Select the blue `Launch` button to launch the instance.

### Create a Key Pair
Not so fast, we still need to create a key pair so we are able to access the instance later.
- Download the private key locally and copy it to the .ssh folder. In my case, and for illustration:

``` $ mv ~/Downloads/***.pem ~/.ssh/***.pem```

- Here \~ means HOME directory. If the .ssh directory does not exist, you can simply create it:

``` $ mkdir -p ~/.ssh```

- Change the permission of the file

``` $ chmod 600 ~/.ssh/***.pem ```

- Remember the key path and name.

## Access your EC2 Instance
- Select the blue `View Instances` button at the bottom of the splash screen

Your ec2 instance is now running.  Hooray!  You should now see a list of all of the instances in the region you launched your instance in.  You might find it useful to tag your instance with a name by clicking to edit the blank `Name` column (see below).

![NameInstance](https://user-images.githubusercontent.com/21269613/110987924-af6ab780-8324-11eb-8d97-33e05a158e23.png)

- Select the white `Connect` button on the Instances toolbar

- Select the `SSH client` to get information for ssh access to your ec2 instance

<img src="https://user-images.githubusercontent.com/21269613/110988775-d70e4f80-8325-11eb-8d1c-2a40e29d08a1.png" align="center" alt="SSHclientInfo" width="700"/>

- Copy the bottom line with the `ssh` command (` ssh -i "kpv-aws-key.pem" ec2-user@ec2-54-71-22-234.us-west-2.compute.amazonaws.com`) 

### Access your EC2 instance through an SSH client
- Launch your SSH client.  For a Mac, this would just be the Terminal.  On Windows, this would be a program like PuTTY

- Paste the `ssh` command from the `SSH client` tab in the AWS console.  Be sure to provide the full path to your key.

```ssh -i "path/to/key/kpv-aws-key.pem" ec2-user@ec2-54-71-22-234.us-west-2.compute.amazonaws.com```

The specific numerical address will be different for every instance.  Additionally the user name (`ec2-user`) may be incorrect if you are loading a custom AMI.  For example, you may need to edit it to `ubuntu` or other some other generic username.

- If prompted to continue connecting, reply `yes` in the terminal.

![SSHaccess](https://user-images.githubusercontent.com/21269613/111039193-a25fce00-83e1-11eb-98f5-eba4e1ee7d2d.png)

If your terminal appears as above, you have successfully logged in to the ec2 instance over SSH!
