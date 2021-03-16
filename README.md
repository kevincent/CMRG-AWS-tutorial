# CMRG-AWS-tutorial
A brief hands on tutorial for CMRG members looking to get familiar with AWS

## Log in to the AWS Console
You will first need to request access to CMRG's AWS account through ITS (see Jen).

Log in using your SSO account at https://awsconsole.ucsd.edu.

## Launch an EC2 Instance
Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud.
 
- Select the `EC2` service which should be the top option below `Compute`.  Take note of which aws region you are in.  **US West(Oregon)`us-west-2`*** is a good region to select for this tutorial.  

- Select the orange `Launch instance` button.

<img src="https://user-images.githubusercontent.com/21269613/110983979-58aeaf00-831f-11eb-8636-6506c6e84c33.png" align="center" alt="SSHclientInfo" width="700"/>

### Select AMI
An Amazon Machine Image (AMI) defines the software configuration (operating system, application server, and applications) that your ec2 instance will run.  If you have specific software you want to run, you may install that software on an ec2 instance and save that installation as an AMI to retrieve for later use.  For this tutorial, the default `Amazon Linux 2 AMI` is sufficient.  

- Hit the blue `Select` button to choose that AMI and continue setting up the ec2 instance.

### Choose your Instance Type
The instance type you select determines the resources you provide your AMI to run with.  The two main features are the number of CPUs and the memory type.  Different computational tasks will have very different needs and require different Instance Types.  The default `t2.micro` is sufficient for this tutorial.

![InstanceType](https://user-images.githubusercontent.com/21269613/110986310-5c900080-8322-11eb-9873-0ffcbd657f07.png)

- Select the blue `Review and Launch` button on the bottom right of the screen

#### A note on cost
The instance type is also the primary determinant also of the ec2 instance cost (hard disk storage and I/O also have expenses, but they are typically smaller).  AWS publishes the cost of ec2 instances (https://aws.amazon.com/ec2/pricing/on-demand/). Consider using spot instance(s) to reduce cost (https://aws.amazon.com/ec2/spot/pricing/).  Know what instance type you need for your task and don't over provision your intance.  Terminate your instance promptly when you are finished with it to avoid unneeded costs.  Be extra careful if you are spinning up an expensive instance type.

### Review the EC2 instance options and Launch the Instance
The next screen will show all of the options selected for the ec2 instance we are about to launch.  There were a number of options we skipped over such as the amount of hard disk storage to provide the instance and whether or not to use hyperthreading. Look over the options on this page to get a feel for the available options.  Many of the options require AWS knowledge that is beyond the scope of this introductory tutorial.

- Select the blue `Launch` button to launch the instance. :tada:

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
- Select the blue `View instances` button at the bottom of the splash screen

Your ec2 instance is now running.  Hooray!  You should now see a list of all of the instances in the region you launched your instance in.  You might find it useful to tag your instance with a name by clicking to edit the blank `Name` column (see below).

![NameInstance](https://user-images.githubusercontent.com/21269613/110987924-af6ab780-8324-11eb-8d97-33e05a158e23.png)

- Select the white `Connect` button on the Instances toolbar

- Select the `SSH client` to get information for ssh access to your ec2 instance

<img src="https://user-images.githubusercontent.com/21269613/110988775-d70e4f80-8325-11eb-8d1c-2a40e29d08a1.png" align="center" alt="SSHclientInfo" width="700"/>

- Copy the bottom line with the `ssh` command (`ssh -i "kpv-aws-key.pem" ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com`) 

### Access your EC2 instance through an SSH client
- Launch your SSH client.  For a Mac, this would just be the Terminal.  On Windows, this would be a program like PuTTY (http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)

- Paste the `ssh` command from the `SSH client` tab in the AWS console.  Be sure to provide the full path to your key.

```$ ssh -i "path/to/key/kpv-aws-key.pem" ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com```

The specific numerical address will be different for every instance.  Additionally the user name (`ec2-user`) may be incorrect if you are loading a custom AMI.  For example, you may need to edit it to `ubuntu` or other some other generic username.

- If prompted to continue connecting, reply `yes` in the terminal.

![SSHaccess](https://user-images.githubusercontent.com/21269613/111039193-a25fce00-83e1-11eb-98f5-eba4e1ee7d2d.png)

If your terminal appears as above, you have successfully logged in to the ec2 instance over SSH.

- To end the SSH connection enter `exit` in the terminal.

## Execute a python script on an EC2 instance
For the next portion of the tutorial, we will copy two files to the ec2 instance that you spun up, run the python script, and copy the result back to your local machine.

- Download the code for this repository to your local machine by clicking the green `Code` button on the top right of the repository homepage.
- Select the `Download Zip` option

![DownloadZip](https://user-images.githubusercontent.com/21269613/111205499-8a747f80-8584-11eb-9e00-088bb9946df9.png)

- Unzip the file on your local machine and take note of its path

### Use SCP to copy a file to the ec2 instance

Next we will copy the two python files to the ec2 instance using `scp`.  Basic `scp` syntax using a key pair is shown below:

```$ scp -i "path/to/key/kpv-aws-key.pem" [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2```

For this specific usage, we are copying the `runAPmodel.py` and `ohara_model.py` files from our local computer to the `ec2-user` account on the ec2 instance.  We will just place the file in the home directory (`.`).

```$ scp -i "path/to/key/kpv-aws-key.pem" path/to/files/runAPmodel.py ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com:.```

You can copy both files at once using the wildcard (`*`) character

```$ scp -i "path/to/key/kpv-aws-key.pem" path/to/files/*.py ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com:~/.```

### Run the python script on the ec2 instance
To run the python script, we need to `ssh` back into the ec2 instance and run it from the command line.

```$ ssh -i "path/to/key/kpv-aws-key.pem" ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com```

### Install Python 3 and dependancies on ec2 instance
First we need to install Python 3 onto the ec2 instance.  Amazon Linux 2 uses the `yum` package manager.  Run the following four lines to install the needed software.

```$  sudo yum install python37```

```$  pip3 install numpy --user```

```$  pip3 install scipy --user```

```$  pip3 install matplotlib --user```

### Run the python script
Now we are ready to run the action potential model.  This is a simulation of the cardiac action potential using the O'Hara-Rudy model (https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1002061).  The simulation is very simple - 10 paced beats at 550ms cycle length.

- Run the simulation with Python 3

```$  python3 runAPmodel.py```

The simulation will print some information to the screen. If the model has run successfully, you will see new files in your directory.  List the files using the `ls` command or `ls -lhrt` for more organized output.

![runScript](https://user-images.githubusercontent.com/21269613/111209635-7c752d80-8589-11eb-838f-6d03c2cd0bd1.png)

We see the two original python files and some new files.  `LastBeat.png` and `AllBeats.png` are plots showing the results of the simulation.  `State_var_final.npy` is a binary numpy file containing the final vector of state variables in the model.

#### Running a script on an ec2 instance when disconnected
The model we have selected for this example runs very quickly, but that is unlikely to be the case if you're offloading your heavy computational tasks to AWS.  If you begin running a task through the terminal (e.g. running a python script or running a simulation on software like Continuity or Browndye), then exit from your ssh connection (or let your computer go to sleep), the process running your task will terminate and your job will not finish running.  You need to find a way to keep your remote SSH process running after disconnection.  You could disown the process using `&` or use one of the command line programs that enable this (tmux, screen, nohup).  I typically use tmux (short for terminal multiplexer) but use whichever method works best for you. I never remember the syntax so I have have a cheatsheet of the useful commands (e.g. https://tmuxcheatsheet.com/).

- Start a new tmux session
```$ tmux new -s mysession```

- run your code as normal in the tmux terminal
```$ python3 runAPmodel.py```

- Detatch from the session
```Ctrl+b d```

- Attach to a session
```$ tmux a``` or ```$ tmux a -t mysession```

### Copy the result back using SCP
Before we can retrieve the figures created from the simulation, we must exit the `ssh` connection.

```$  exit```

To retrieve the output file, we reverse the source and destination of the `scp` command.  First, end the `ssh` connection using 

```scp -i "path/to/key/kpv-aws-key.pem" ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com/:AllBeats.png local/path/.```

If you want to copy multiple files at a time, you can again use the wildcard character (`*`).  However, when using the wildcard with a remote host, you need to prefix it with a forward slash (`\`).

```scp -i "path/to/key/kpv-aws-key.pem" ec2-user@ec2-##-##-##-###.us-west-2.compute.amazonaws.com/:\*.png local/path/.```

The files are now back on your local machine for further analysis, visualization, etc. :tada:

### Running a script stored in an S3 bucket
For an ec2 instance to access an S3 bucket, the instance requires an IAM role with sufficient priveldeges (https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-access-s3-bucket/). Currently this is not possible with AWS access using your UCSD SSO account.  We are working to get this available.

## Terminating an EC2 instance
The final step is to shut down or terminate the ec2 instance. *Terminating the instance is very important as you will keep incuring charges until your instance is terminated!* Remember to be extra careful to terminate an instance if it is using an expensive instance type.

When you terminate an instance, **all of the data is deleted** so be sure to copy your data off the instance (using `scp`) or store data you need to access again into an S3 bucket.

- Press the white `Instance state` button on the Instances toolbar
- Select `Terminate instance` option in the menu (see below)
- Press the orange `Terminate` button

![TerminateInstance](https://user-images.githubusercontent.com/21269613/111191060-e9ca9380-8574-11eb-9509-88c24800eeb3.png)

### Stoping an EC2 instance
As you can see from the menu above, terminating the instance isn't the only option.  You could also select `Stop instance`.  When you stop an instance, you dissociate the volume from the compute resource.  This keep anything that was saved to the hard disk but loses anything that was stored in temporary memory.  When an instance is stopped, you are no longer charged the hourly charge for the instance type, but you still pay the monthly charge for the memory.  You can see stopped volumes by selecting the `Volumes` option under `Elastic Block Store` on the lefthand navigational panel in the EC2 console. 

Stopping an instance can be useful in narrow circumstances - say you have a lot of data or software on an instance, but you don't need it for a short period of time (days).  That will prevent the hourly instance type charges from racking up.

### Saving an AMI
If your work requires custom software (e.g. Browndye, Continuity, Cufflinks, etc.), you can instal the software on an ec2 instance through the terminal when you have ssh access. Installing the proper software can be time consuming and/or challenging. It would be tedious to re-install the software every time you wanted to use AWS.  One solution to this is to save the state of your EC2 instance as an AMI after installing the software.  Future instances can be spun up using that AMI.

To create an AMI from an ec2 instance:
- Select the white `Actions` button on the Instances toolbar 
- `Actions` -> `Images and templates` -> `Create image`

![CreateAMI](https://user-images.githubusercontent.com/21269613/111049154-507b7000-8402-11eb-8969-f888a776d8d7.png)
Remember, AMIs are region specific in AWS.

Saving a static AMI can cause problems because the OS no longer receives updates, and some services (e.g. AWS ParallelCluster) might not run with an out of date AMI.  To avoid this, you could deploy a docker container using AWS Elastic Container Service (https://aws.amazon.com/getting-started/hands-on/deploy-docker-containers/).  Another AWS solution might be to use EC2 Image Builder, but I have never used Image Builder myself.

*Update the tutorial if you give ECS or Image Builder a try*

## AWS ParallelCluster
If you need to run many simulations or very computationally expensive simulations, AWS ParallelCluster can provide an HPC enviroment. ParallelCluster allows you to submit jobs to a scheduler (e.g. slurm, aws batch | sge support will be depricated).  ParallelCluster will spin up as many instances as needed to farm out large batches of jobs.  ParallelCluster requires AWS CLI which is not currently available through the UCSD SSO login, but this should be requested.  A explaination of ParallelCluster is beyond the scope of this tutorial, but below are a few links to get you started.

- https://www.hpcworkshops.com/03-hpc-aws-parallelcluster-workshop.html
- https://docs.aws.amazon.com/parallelcluster/latest/ug/what-is-aws-parallelcluster.html
- https://aws.amazon.com/hpc/parallelcluster/
- https://www.youtube.com/watch?v=UnQM7cX2y0E
- https://jiaweizhuang.github.io/blog/aws-hpc-guide/#pcluster-ami

## Other AWS Links

https://github.com/kevincent/Continuity_w_AWS

The above repo hosts a Jupyter Notebook capable of running Continuity using AWS CLI.  This requires CLI credentials not currently available through UCSD's SSO console.  

https://www.coursera.org/learn/aws-cloud-practitioner-essentials

If you want a strong overview of all of AWS' services and be able to speak the language of AWS there is a nice free Coursera course. 
