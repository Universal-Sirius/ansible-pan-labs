![Shape4](RackMultipart20221031-1-3afzz8_html_51b5761ce0d8ce33.gif) ![Shape2](RackMultipart20221031-1-3afzz8_html_4d2aa215e8a3dff2.gif) ![Shape3](RackMultipart20221031-1-3afzz8_html_56c6b91a2154af8.gif) ![Shape1](RackMultipart20221031-1-3afzz8_html_54b7cf8a4afa4815.gif)

Rich Mallory

rich.mallory@siriuscom.com

Abstract

This Sirius Ansible Immersion Day Workshop will introduce using Ansible with the Palo Alto firewall.

# Sirius Ansible Palo Alto Immersion Day Workshop

# Table of Contents

**[Part 1A - Initial Lab Setup 2](#_Toc118035506)**

**[Part 1B - Connect to Palo Alto firewall via SSH and set your username/password 5](#_Toc118035507)**

**[Part 2 - Ansible Labs 8](#_Toc118035508)**

**[Lab 1.0: Explore your Lab Environment 9](#_Toc118035509)**

**[Lab Prep 12](#_Toc118035510)**

**[Review the variables file 12](#_Toc118035511)**

**[Lab 1.1: Gather Facts from your Palo Alto Firewall 12](#_Toc118035512)**

**[Lab 1.2: Preform a Palo Alto Firewall Base Configuration 14](#_Toc118035513)**

**[Lab 1.3: Add logging server profile 14](#_Toc118035514)**

**[Lab 1.4: Add a Simple Security Rule 16](#_Toc118035515)**

**[Lab 1.5: Adding Bulk Security Rules from a CSV file 16](#_Toc118035516)**

**[Lab 1.6: Remove Address Object 18](#_Toc118035517)**

**[Lab 1.7: Backup Palo Alto Firewall Configuration 19](#_Toc118035518)**

**Overview Palo Alto Immersion Day Workshop**

You will use Ansible commands and playbooks to explore and reconfigure a Palo Alto in a virtual environment. Note, that even though we are using a virtual environment, this is not a requirement, everything we do, can be done on physical devices.

The Immersion Day is meant as a follow on to the Network Automation Immersion Day. Some content will be review but in respect to using Ansible with Palo Alto. While you can go through these labs there may be concepts that were covered previously that we will not cover as it is expected that these concepts are already understood. Throughout the labs we hope to share some additional features, elements, good and bad practices, and patterns to using Ansible for Automation.

You will be required to modify some files during this workshop. You will not be required to write your own playbooks as this would require much more time. The playbooks used are open source and thus free to use and modify. Writing playbooks and running them in a test environment is one of the best ways to learn.

_Note: The output shown in the examples may vary from yours._

## Part 1A - Initial Lab Setup

At the end of this section the lab github repo will be forked into your own github account. You will access the jump station and download the newly forked github repo.

1. Fork the lab github repository to your own repository so you can edit and modify.
2. Access the jump station
3. Download the forked repository to the jump station

### Step 1 - Fork the Sirius ansible Pan Labs Github repository

- Login in to Github at [https://github.com](https://github.com/)
- Go to [https://github.com/mysidlabs/pan-labs-ee-1](https://github.com/mysidlabs/pan-labs-ee-1)
- Click on the Fork button in upper right.

![](RackMultipart20221031-1-3afzz8_html_2ded79c437ff6edf.png)

_Note: Once forked you can modify view and modify all files within your GitHub_

### Step 2 - Connect to the Jump host

- SSH to the jump station at[**jump.mysidlabs.com**](http://jump.mysidlabs.com/)

**For MacOS or Linux users the following is an example using the terminal:**

**$ ssh** siduser\<\<ID\>\>@jump.mysidlabs.com

Ex. $ssh [siduser101@jump.mysidlabs.com](mailto:siduser101@jump.mysidlabs.com)

**You may get the following message, type** yes **at the prompt:**

The authenticity of host 'jump.mysidlabs.com (3.132.28.93)' can't be established.

ECDSA key fingerprint is SHA256: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Are you sure you want to continue connecting (yes/no/[fingerprint])? **Yes**

**Warning: Permanently added 'jump.mysidlabs.com,3.132.28.93' (ECDSA) to the list of known hosts.**

Note: You can remove from known hosts after workshop is completed.

When prompted for your password type in the password the instructor provides

password: \*\*\*\*\*\*\*

For Windows users the following is an example using Putty:

Type jump.mysidlabs.com in the Host Name box and click the Open button

![](RackMultipart20221031-1-3afzz8_html_4ec67413cb5095a.png)

Click the **Yes** button to accept the ssh key

![](RackMultipart20221031-1-3afzz8_html_574012559668b54e.png)

**Type in username and password in the terminal screen at the appropriate prompts**

![](RackMultipart20221031-1-3afzz8_html_7d6c6c435dbd4702.png)

### Step 3 - Download forked repository to the jump station

1. Your terminal prompt should change to something like the following:

**siduser101@jump:~$**

4. Clone your repository

**siduser101@jump ~ #**  **git clone** [https://github.com/\<\<YOUR\_GITHUB\_USER\>\>/pan-labs-ee-1](https://github.com/%3C%3CYOUR_GITHUB_USER%3E%3E/pan-labs-ee-1)

| Tip | The usage of git becomes very important to "infrastructure as code". Everything resides in github including your changes. If you lose connection from the jump box, the repository will be deleted automatically. All you need to do is clone your repository and you are back to where you were. |
| --- | --- |

5. You should now see the repository in your directory

**siduser101@jump:~$** ls

pan-labs-ee-1

6. Move into the pan-labs-ee-1directory

siduser101@jump ~ # **cd**  **pan-labs-ee-1**

siduser101@jump:~/pan-labs-ee-1$

Additional Information

You can now explore the labs directory

cd = change directory

ls = list contents

pwd = display current working directory

cat = display file

nano or vim = file editor

tree = display file structure from current directory

_Note: At this point your jump host is setup is complete and is ready to use_

## Part 1B - Connect to Palo Alto firewall via SSH and set your username/password

This section will accomplish the following two tasks:

1. Connect to your firewall via SSH and add new admin user to use for ansible playbooks.
2. Connect to your firewall via HTTPS with your new admin username/password.

Your student Palo Alto Firewall does not have a username and password configured yet. Access the firewall and set a username and password. Replace \<\<ID\>\> with your student number.

### Step 1 - Connect to FW via ssh from jump host.

1. **ssh -i ~/.ssh/network-key.pem admin@siduser\<ID\>.pan.mysidlabs.com**

load pubkey "/home/siduser101/.ssh/network-key.pem": invalid format

The authenticity of host 'siduser101.pan.mysidlabs.com (3.133.142.207)' can't be established.

RSA key fingerprint is SHA256:tfEPSNM5pDbgEOEKv5H059pu1uK8l5T2QjcLIDU03eE.

Are you sure you want to continue connecting (yes/no/[fingerprint])? **yes**

Warning: Permanently added 'siduser101.pan.mysidlabs.com,3.133.142.207' (RSA) to the list of known hosts.

Welcome admin.

admin@PA-VM\>

1. Go into configuration mode

admin@PA-VM\> **configure**

1. Add a new user

admin@PA-VM# **set mgt-config users siduser\<ID\> password**

Enter password :

Confirm password :

1. Give your new user admin permissions

admin@PA-VM# **set mgt-config users siduser\<ID\> permissions role-based superuser yes**

1. Commit Palo Alto changes

admin@PA-VM# **commit**

Commit job 2 is in progress. Use Ctrl+C to return to command prompt

.......55%98%..............100%

Configuration committed successfully

1. Exit from Firewall

Type **exit** twice to disconnect from your student Palo Alto Firewall

admin@PA-VM# **exit**

Exiting configuration mode

admin@PA-VM\> **exit**

Connection to siduser101.pan.mysidlabs.com closed.

**siduser101@jump:~/pan-labs-ee-1$**

### Step 2 - Connect to Palo Alto via HTTPS with new username and password

##

  1.
### Open a web browser and connect to https://siduser\<\<ID\>\>.pan.mysidlabs.com

![](RackMultipart20221031-1-3afzz8_html_9f5477d21efa47dc.png)

  1.
### Click Advanced

![](RackMultipart20221031-1-3afzz8_html_ee4dc86194bf5e61.png)

  1.
### Click "Proceed to siduser\<\<ID\>\>.pan.mysidlabs.com (unsafe)

  1.
### Login with your credentials, verify you can see the PAN dashboard

## Part 2 - Ansible Labs

### Lab Topology

The topology is simple for the sake of learning some ansible basics. The diagram below is an example, the XXX in the hostname is your Student ID. If you are student 199 then the hostname for the PaloAlto would be siduser199.pan.mysidlabs.com.

![](RackMultipart20221031-1-3afzz8_html_5001a8224062b45c.jpg)

## Lab 1.0: Explore your Lab Environment

### **Step 1** - Make sure you are in the pan-labs-ee-1 folder

siduser101@jump:~/pan-labs-ee-1$ **pwd**

/home/siduser101/pan-labs-ee-1

If you are not, change to the pan-labs-ee-1 directory

siduser101@jump:~ # **cd ~/pan-labs-ee-1**

**Step 2** - Review the ansible-navigator.yml file

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

**Step 3** - Review the currently installed execution environments

siduser101@jump:~/pan-labs-ee-1$ **podman images**

REPOSITORY TAG IMAGE ID CREATED SIZE

**Step 4** - Run Ansible Navigator

siduser101@jump:~/pan-labs-ee-1$ **ansible-navigator**

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

\<\< Output Ommitted \>\>

**Step 5** - Pull in the execution environment

siduser101@jump:~/pan-labs-ee-1$ **podman pull ghcr.io/mysidlabs/panlab-ee-1**

Trying to pull ghcr.io/mysidlabs/panlab-ee-1:latest...

Getting image source signatures

Copying blob 01530b510795 done

Copying blob 894369c521e6 done

\<\< Output Omitted \>\>

siduser101@jump:~/pan-labs-ee-1$ cd project/

siduser101@jump:~/pan-labs-ee-1/project$

**Step 6** - Run Ansible Navigator

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator --mode interactive**

![](RackMultipart20221031-1-3afzz8_html_f68de7a75ac39960.png)

Commands to try from within the ansible-navigator interface

**:images**

![](RackMultipart20221031-1-3afzz8_html_57d4a4ed051f9e5f.png)

Type **1** to select the panlab-ee-1 image

![](RackMultipart20221031-1-3afzz8_html_a19dec118cfeb12b.png)

Type the **#2** to review the Ansible version and collection

![](RackMultipart20221031-1-3afzz8_html_e833501050636133.png)

Escape to exit the screen

Type **0** to review the image Information

![](RackMultipart20221031-1-3afzz8_html_da7ca2631a32e92a.png)

## Lab Prep

##

Step 1 - Review the variables file:

siduser101@jump:~/pan-labs-ee-1/project$ **cat group\_vars/all.yml**

provider:

username: 'siduser101'

password: 'Ans1ble!'

ip\_address: siduser101.pan.mysidlabs.com

Step 2 - Change the variables to match the user you setup in Part 1B

siduser101@jump:~/pan-labs-ee-1/project$ **nano group\_vars/all.yml**

## Lab 1.1: Gather Facts from your Palo Alto Firewall

Lab Goals:

1. Show how to gather facts from a Palo Alto firewall. It will introduce the use of roles which will be used for most playbooks in this lab.
2. We will review the JSON output from the panos\_facts module and show how to filter and display specific information.

### Step 1 – Review the first playbook

Look at the file (ansible playbook) called 1.1\_palo\_facts.yml.

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.1\_palo\_facts.yml**

**---**

**- hosts: localhost**

**connection: local**

**gather\_facts: False**

**tasks:**

**- include\_role:**

**name: palo\_facts**

**...**

### Step 2 – Take a look at the roles playbook for gathering Palo Alto firewall facts

Ansible playbooks are YAML files. YAML is a structured encoding format that is also extremely human readable. In this case we are using "include\_role:" explor the role palo\_facts.

siduser101@jump:~/pan-labs-ee-1/project **tree roles/palo\_facts/**

![](RackMultipart20221031-1-3afzz8_html_62e6e41af3794cbb.png)

siduser101@jump:~/pan-labs-ee-1/project$ **cat roles/palo\_facts/tasks/main.yml**

**\<\< output omitted \>\>**

siduser101@jump:~/pan-labs-ee-1/project$ **cat roles/palo\_facts/tasks/palo\_facts.yml**

**\<\< output omitted \>\>**

| Tip | Notice the FQCN or fully qualified collection name paloaltonetworks.panos.panos\_facts. This format is required when using roles with collections that are not installed in engine by default such as Palo Alto collections used in this lab. |
| --- | --- |

siduser101@jump:~/pan-labs-ee-1/project$ **cat roles/palo\_facts/tasks/palo\_vr\_facts.yml**

**\<\< output omitted \>\>**

### Step 3 – Run the gather facts playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1.1\_palo\_facts.yml**

**\<\< output omitted \>\>**

### Step 4 – Review the output of the playbook

Review the output.

Can you think of some use cases for this role?

## Lab 1.2: Preform a Palo Alto Firewall Base Configuration

### Lab Goals:

1. Review and use role variables in a role
2. Complete the initial configuration setup using Ansible for a Palo Alto firewall.

### Step 1 – Review the Palo Alto Initial Setup Ansible Playbook

Review the file called 1.2\_palo\_initial\_setup.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.2\_palo\_initial\_setup.yml**

**\<\< output omitted \>\>**

explore the role

siduser101@jump:~/pan-labs-ee-1/project$ **tree roles/initial\_palo\_setup/**

**\<\< output omitted \>\>**

_Note: Notice that there is a vars directory. This is known as roles vars_

siduser101@jump:~/pan-labs-ee-1/project$ **cat roles/initial\_palo\_setup/vars/main.yml**

**\<\< output omitted \>\>**

| Tip | When a variable file exists in the roles directory structure in the vars folder and is called "main.yml" it will be called into the playbook by default, there is no need to reference a specific vars file in the playbook. This is useful because all the vars required for the specific role are located with the role. This can make modular playbook design using roles easier. |
| --- | --- |

### Step 2 – Run the Initial Setup Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1. 2\_palo\_initial\_setup.yml**

**\<\< output omitted \>\>**

### Step 3 – Verify the Palo Alto Configuration

Verifying that the playbook did what you expected. Login to the Palo Alto with your web browser to see what was configured. https://siduser\<\<ID\>\>.pan.mysidlabs.com

## Lab 1.3: Add logging server profile

### Lab Goals:

1. Add a logging server profile to the firewall

### Step 1- Review the Palo Alto Logging Setup Ansible Playbook

Look at the file called 1.3\_logging\_setup.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.3\_logging\_setup.yml**

**\<\< output omitted \>\>**

siduser101@jump:~/pan-labs-ee-1/project$ **tree roles/palo\_log\_server/**

**\<\< output omitted \>\>**

siduser101@jump:~/pan-labs-ee-1/project$ **tree roles/palo\_syslog/**

**\<\< output omitted \>\>**

_Note: Use GitHub to look at the roles and roles vars_

### Step 2 – Run the Palo Alto Logging Setup Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1.3\_logging\_setup.yml**

**\<\< output omitted \>\>**

### Step 3 – Complete Verification

Verifying that the playbook did what you expected. Login to the Palo Alto with your web browser to see what was configured.

## Lab 1.4: Add a Simple Security Rule

_Note: This lab is required prior to lab 1.5. It adds a deny all rule to the end of the security policy which is referenced in the next playbook._

Lab Goals:

1. Add a Deny All rule to the end of the security policy.

### Step 1 – Review the Playbook

Look at the file called 1.4\_simple\_security\_rule\_add.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.4\_simple\_security\_rule\_add.yml**

_Note: This playbook does not use a role. We did this to illustrate how to use collections in a playbook where roles are not included._

### Step 2 – Run the Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ # **ansible-navigator run**** 1.4\_simple\_security\_rule\_add.yml**

**\<\< output omitted \>\>**

### Step 3 – Complete Verification

Verifying that the playbook did what you expected. Login to the Palo Alto with your web browser to see what was configured.

## Lab 1.5: Adding Bulk Security Rules from a CSV file

### Lab Goals:

1.
### Use a CSV file to add a bulk set of security rules to the firewall

### Step 1 - Review the Playbook

Look at the file called 1.5\_add\_rules\_csv.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.5\_add\_rules\_csv.yml**

### Step 2- Review the Playbook Files

Use cat or github to review the roles tasks, roles variables and the roles files.

_Note: This playbook pulls variables from a CSV file._

### Step 3 – Run the Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1.5\_add\_rules\_csv.yml**

**\<\< output omitted \>\>**

### Step 4 - Complete Verification

Verifying that the playbook did what you expected. Login to the Palo Alto with your web browser to see what was configured.

Bonus Task!

Edit the CSV file and add additional rules by running the playbook again.

## Lab 1.6: Remove Address Object

### Lab Goals:

1.
### Remove an address object from the firewall.

### Step 1 – Review the Playbook

Look at the file called 1.6\_remove\_address\_object.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.6\_remove\_address\_object.yml**

### Step 2 - Review the Playbook Associated Files

Use cat or github to review the roles tasks and roles variables

### Step 3 - Edit Variable File

Modify the variable file to a server of your choosing using nano, vi or github

Example

remove\_addr: 'server\_6'

### Step 4 – Run the Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1.6\_remove\_address\_object.yml**

**\<\< output omitted \>\>**

### Step 5 – Complete Playbook Verification

Verifying that the playbook did what you expected. Login to the Palo Alto with your web browser to see what was configured.

Why is the rule greyed out?

Can you think of some use cases for this role?

## Lab 1.7: Backup Palo Alto Firewall Configuration

### Lab Goals:

1. Complete a backup of the current Palo Alto configuration.

### Step 1 - Review the Playbook

Look at the file called 1.7\_palo\_backup\_configuration.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.7\_palo\_backup\_configuration.yml**

### Step 2 - Review the Playbook Associated Files

Review the roles tasks and roles variables with cat or GitHub. The variable file should look like below.

---

## Be sure path is writable

backup\_path : '~/ansible-pan-labs/'

...

### Step 3 – Run the Backup Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run**** 1.7\_palo\_backup\_configuration.yml**

**\<\< output omitted \>\>**

### Step 4 – Review the Backup File

**Ensure that the backup file exists. Copy the backup file name**

Can you think of some use cases for this role?

Lab 1.8: Restore the configuration

### Lab Goals:

1.
### Restore a previously backed up configuration to the firewall.

### Step 1 - Review the Playbook

Look at the file called 1.8\_palo\_restore\_configuration.yml

siduser101@jump:~/pan-labs-ee-1/project$ **cat 1.8\_palo\_restore\_configuration.yml**

### Step 2 - Review the Playbook Associated Files

Review the roles variables with cat or github. The variable file should look like below.

---

backup\_path : 'home/siduser\<ID\>/ansible-pan-labs/'

restore\_file: 'backup-2021-01-01-00-01.xml'

...

Edit the backup\_path variable to have your siduser ID.

Edit the restore\_file variable to be your saved backup file name.

### Step 3 – Run the Restore Playbook

Run the playbook:

siduser101@jump:~/pan-labs-ee-1/project$ **ansible-navigator run 1.8\_palo\_restore\_configuration.yml**

**\<\< output omitted \>\>**

Success – Congratulations for Completing!

Key Lab Takeaways

1. Remember YAML is very sensitive to correct indentation.
2. The use of an Ansible role is best practice when there is a well-defined scope with a high possibility of re-use.
3. Plan to the location of variables carefully. Never have a single variable in more than one location.
4. If you copy and paste text for a playbook you may get indentation issues. Ansible provides a simple syntax checker, try ansible-playbook --syntax-check backup.yml to verify. A Best Practice is to use a linter, for example ansible-review.
5. Ansible provides excellent online documentation, which is also available from the command line, for example ansible-doc ansible.netcommon.cli\_command. For a full list of modules try ansible-doc –-list

## **Appendix A:**

Useful resource links and information

Links:

Ansible Best Practices:

[https://docs.ansible.com/ansible/latest/user\_guide/playbooks\_best\_practices.html](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)

Variable precedence:

[https://docs.ansible.com/ansible/latest/user\_guide/playbooks\_variables.html#variable-precedence-where-should-i-put-a-variable](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

Ansible Modules

[https://docs.ansible.com/ansible/2.9/modules/modules\_by\_category.html](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

Ansible Galaxy

[https://galaxy.ansible.com/home](https://galaxy.ansible.com/home)

Ansible Palo Alto Module Reference

[https://ansible-pan.readthedocs.io/en/latest/modules/index.html](https://ansible-pan.readthedocs.io/en/latest/modules/index.html)

Ansible Navigator Creator Guide

[https://access.redhat.com/documentation/en-us/red\_hat\_ansible\_automation\_platform/2.1/html/ansible\_navigator\_creator\_guide/index](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.1/html/ansible_navigator_creator_guide/index)