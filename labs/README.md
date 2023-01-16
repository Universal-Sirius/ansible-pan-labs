### Sirius Ansible Palo Alto
Immersion Day Workshop

Rich Mallory
rich.mallory@siriuscom.com

Abstract

This Sirius Ansible Immersion Day Workshop will introduce using Ansible with the Palo Alto
firewall.

**Table of Contents**

Part 1A - Initial Lab Setup...........................................................................................2
Part 1B - Connect to Palo Alto firewall via SSH and set your username/password.............................4
Part 2 - Ansible Labs..................................................................................................7
Lab 1.0: Explore your Lab Environment......................................................................................8
Lab Prep............................................................................................12
Review the variables file...............................................................................................12
Lab 1.1: Gather Facts from your Palo Alto Firewall.......................................................................12
Lab 1.2: Preform a Palo Alto Firewall Base Configuration ......................................................14
Lab 1.3: Add logging server profile ................................................................................................14
Lab 1.4: Add a Simple Security Rule...............................................................................................16
Lab 1.5: Adding Bulk Security Rules from a CSV file........................................................................16
Lab 1.6: Remove Address Object .........................................................................................18
Lab 1.7: Backup Palo Alto Firewall Configuration .................................................................19


### Overview Palo Alto Immersion Day Workshop

You will use Ansible commands and playbooks to explore and reconfigure a Palo Alto in a virtual
environment. Note, that even though we are using a virtual environment, this is not a requirement,
everything we do, can be done on physical devices.


The Immersion Day is meant as a follow on to the Network Automation Immersion Day. Some content will
be review but in respect to using Ansible with Palo Alto. While you can go through these labs there may
be concepts that were covered previously that we will not cover as it is expected that these concepts are
already understood. Throughout the labs we hope to share some additional features, elements, good and
bad practices, and patterns to using Ansible for Automation.


You will be required to modify some files during this workshop. You will not be required to write your own
playbooks as this would require much more time. The playbooks used are open source and thus free to
use and modify. Writing playbooks and running them in a test environment is one of the best ways to
learn.

**Note: The output shown in the examples may vary from yours.**

*Part 1A - Initial Lab Setup*

At the end of this section the lab github repo will be forked into your own github account. You will access
the jump station and download the newly forked github repo

1. Fork the lab github repository to your own repository so you can edit and modify.
2. Access the jump station
3. Download the forked repository to the jump station

**Step1** - Fork the Sirius ansible Pan Labs Github repository

• Login in to Github at https://github.com

• Go to https://github.com/mysidlabs/pan-labs-ee-1

• Click on the Fork button in upper right.

![](images/image1.png)

***Note: Once forked you can modify view and modify all files within your GitHub***

**Step2** - Connect to the Jump host

• SSH to the jump station at jump.mysidlabs.com

**For MacOS or Linux users the following is an example using the terminal:**

    $ ssh siduserxxx@siduserxxx.jump.mysidlabs.com
    Ex. $ssh siduser101@siduser101.jump.mysidlabs.com


**You may get the following message, type yes at the prompt:**

The authenticity of host 'jump.mysidlabs.com (3.132.28.93)' can't be established.
ECDSA key fingerprint is SHA256: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Are you sure you want to continue connecting (yes/no/[fingerprint])? Yes
**Warning: Permanently added 'siduser101.jump.mysidlabs.com,3.132.28.93' (ECDSA) to the
list of known hosts.**

Note: You can remove from known hosts after workshop is completed.
When prompted for your password type in the password the instructor provides
password: *******


For Windows users the following is an example using Putty:
Type jump.mysidlabs.com in the Host Name box and click the Open button

![](images/image2.png)


Click the Yes button to accept the ssh key 

![](images/image3.png)

**Type in username and password in the terminal screen at the appropriate prompts** 

![](images/image4.png)

**Step 3 - Download forked repository to the jump station**

1. Your terminal prompt should change to something like the following:
 siduser101@jump:~$
2. Clone your repository
 siduser101@jump ~ # git clone https://github.com/<<YOUR_GITHUB_USER>>/pan-labs-ee-1
Tip
The usage of git becomes very important to “infrastructure as code”. Everything resides in github
including your changes. If you lose connection from the jump box, the repository will be deleted
automatically. All you need to do is clone your repository and you are back to where you were.
3. You should now see the repository in your directory
 siduser101@jump:~$ ls
 pan-labs-ee-1
4. Move into the pan-labs-ee-1directory
siduser101@jump ~ # cd pan-labs-ee-1
siduser101@jump:~/pan-labs-ee-1$

**Additional Information**

    You can now explore the labs directory

    cd = change directory

    ls = list contents

    pwd = display current working directory

    cat = display file

    nano or vim = file editor

    tree = display file structure from current directory
    
**Note: At this point your jump host is setup is complete and is ready to use**

### Part 1B - Connect to Palo Alto firewall via SSH and set your username/password


This section will accomplish the following two tasks:
1. Connect to your firewall via SSH and add new admin user to use for ansible playbooks.
2. Connect to your firewall via HTTPS with your new admin username/password.

Your student Palo Alto Firewall does not have a username and password configured yet. Access the
firewall and set a username and password. Replace <<ID>> with your student number.
    
    
**Step 1 - Connect to FW via ssh from jump host.**

    1. ssh -i ~/.ssh/network-key.pem admin@siduser<ID>.pan.mysidlabs.com
    load pubkey "/home/siduser101/.ssh/network-key.pem": invalid format
    The authenticity of host 'siduser101.pan.mysidlabs.com (3.133.142.207)' can't be established.
    RSA key fingerprint is SHA256:tfEPSNM5pDbgEOEKv5H059pu1uK8l5T2QjcLIDU03eE.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added 'siduser101.pan.mysidlabs.com,3.133.142.207' (RSA) to the list of
    known hosts.

    Welcome admin.
    admin@PA-VM>
    2. Go into configuration mode
    admin@PA-VM> configure
    3. Add a new user
    admin@PA-VM# set mgt-config users siduser<ID> password
    Enter password :
    Confirm password :
    4. Give your new user admin permissions
    admin@PA-VM# set mgt-config users siduser<ID> permissions role-based superuser yes
    5. Commit Palo Alto changes
    admin@PA-VM# commit
    Commit job 2 is in progress. Use Ctrl+C to return to command prompt
    .......55%98%..............100%
    Configuration committed successfully
    6. Exit from Firewall
    Type exit twice to disconnect from your student Palo Alto Firewall
    admin@PA-VM# exit
    Exiting configuration mode
    admin@PA-VM> exit
    Connection to siduser101.pan.mysidlabs.com closed.
     siduser101@jump:~/pan-labs-ee-1$

**Step 2 - Connect to Palo Alto via HTTPS with new username and password**

    1. Open a web browser and connect to https://siduser<<ID>>.pan.mysidlabs.com 
    
![](images/image4.png) 

    2. Click Advanced
    
![](images/image5.png) 

    3. Click “Proceed to siduser<<ID>>.pan.mysidlabs.com (unsafe)
    
    4. Login with your credentials, verify you can see the PAN dashboard
    

# Part 2 - Ansible Labs

### Lab Topology

The topology is simple for the sake of learning some ansible basics. The diagram below is an example,
the XXX in the hostname is your Student ID. If you are student 199 then the hostname for the PaloAlto
would be siduser199.pan.mysidlabs.com. 


![](images/image6.png) 

## Lab 1.0: Explore your Lab Environment

**Step1** 

Make sure you are in the pan-labs-ee-1 folder

    siduser101@jump:~/pan-labs-ee-1$ pwd
    /home/siduser101/pan-labs-ee-1
    If you are not, change to the pan-labs-ee-1 directory
    siduser101@jump:~ # cd ~/pan-labs-ee-1
    
**Step2**

Review the ansible-navigator.yml file

    siduser101@jump:~/pan-labs-ee-1/project$ cat ansible-navigator.yml
    ---
    ansible-navigator:
     ansible:
     inventory:
     entries:
     - inventory
     execution-environment:
     container-engine: podman
     enabled: true
     image: localhost/pan-lab-ee-1:latest
     pull:
     policy: missing
     mode: stdout
     playbook-artifact:
     enable: false
     
**Step3**

Review the currently installed execution environments

    siduser101@jump:~/pan-labs-ee-1$ podman images
    REPOSITORY TAG IMAGE ID CREATED SIZE
    
**Step4**    

    Run Ansible Navigator

    siduser101@jump:~/pan-labs-ee-1$ ansible-navigator
    --------------------------------------------------------------------------------------------------------------
    Execution environment image and pull policy overview
    --------------------------------------------------------------------------------------------------------------
    Execution environment image name: registry.redhat.io/ansible-automation-platform-22/ee-supported-rhel8:latest
    Execution environment image tag: latest
    Execution environment pull arguments: None
    Execution environment pull policy: tag
    Execution environment pull needed: True
    --------------------------------------------------------------------------------------------------------------
    Updating the execution environment
    --------------------------------------------------------------------------------------------------------------
    Running the command: podman pull registry.redhat.io/ansible-automation-platform-22/ee-supported-rhel8:latest
    Trying to pull registry.redhat.io/ansible-automation-platform-22/ee-supported-rhel8:latest...
    Getting image source signatures
    Checking if image destination supports signatures
    Copying blob 2dfca0f3f79a done
    Copying blob d5d2e87c6892 done
    << Output Ommitted >>


**Step5** 

Pull in the execution environment

    siduser101@jump:~/pan-labs-ee-1$ podman pull ghcr.io/mysidlabs/panlab-ee-1
    Trying to pull ghcr.io/mysidlabs/panlab-ee-1:latest...
    Getting image source signatures
    Copying blob 01530b510795 done
    Copying blob 894369c521e6 done
    << Output Omitted >>
    siduser101@jump:~/pan-labs-ee-1$ cd project/
    siduser101@jump:~/pan-labs-ee-1/project$
    

**Step6**

Run Ansible Navigator

    siduser101@jump:~/pan-labs-ee-1/project$ ansible-navigator --mode interactive 



![](images/image7.png)

Commands to try from within the ansible-navigator interface 

:images

![](images/image8.png)


Type 1 to select the panlab-ee-1 image 

![](images/image10.png)

Type the #2 to review the Ansible version and collection 

![](images/image11.png)

Escape to exit the screen

Type 0 to review the image Information

![](images/image12.png)



