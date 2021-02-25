# How to use Ansible playbooks for configuring the Kubernetes cluster on AWS?
I have created some playbooks for creating the cluster on AWS with Ansible. To do it right just follow these steps:
## Step 1: Provision AWS EC2 instance.
For provisioning, Ansible provides the ec2 module of AWS. It requires an AWS IAM user access key and secret key, we can get it from the IAM service of AWS if we have an AWS account. After getting the credentials, we can store them as environmental variables or we can use Ansible-vault to encrypt them. In this example, I have used Ansible-vault to provide more security. Here is a step to create it:
### Ansible vault

	*ansible-vault create secret.yml*<br>
	New Vault password:<br> 
	Confirm New Vault password:<br>
	
After running the above command, it will ask you to create a password and then it will open a vi editor to edit the file. Just enter the access key and secret key in YAML format. Like this:<br>

	access_key: <access_key>
	secret_key: <secret_key>

Now, Our Ansible-vault is ready to be used.<br>
Use the vault's variable like this in the playbook.<br>

	- hosts: localhost
	  vars_files: 
	  - secret.yml
	  ec2:
	    aws_access_key: "{{ access_key }}"
	    aws_secret_key: "{{ secret_key }}"
	    
I have already created a YAML file called ___ec2_provisioning.yml___. It will launch the instances. For increase or decrease, the number of instances edits the *count: <number>* inside the playbook and run the playbook with *ansible-playbook --vault-id @prompt ec2_provisioning* and enter the password of your vault.

## Step 2: Dynamic Inventory
I have already uploaded the files for creating the dynamic inventory inside the folder called **hosts**, and we will mention it in the **ansible.cfg** file at __inventory=hosts__. In the hosts folder, we have the **ec2.py** and **ec2.ini** file, that will going to help us to find the IP of the instances from the AWS cloud.

	export AWS_ACCESS_KEY_ID=<'access-key'>
	export AWS_SECRET_ACCESS_KEY=<'secret-key'>
	export ANSIBLE_HOSTS=<path/of/ec2.py>
	export EC2_INI_PATH=<path/of/ec2.ini>

The above command will export the environmental variables. These variable helps to find the necessary things for ec2.py file to run.

	./ec2.py --list

The above command will list all the instances by tag name.

## Step 3: Step up the master node and worker node.
Now, all the pre-requisite has been done. Now Just execute the **kube-maste.yml**, it will print the token of master node. Copy that token and paste it while execution of file **kube-worker.yml**. These file will configure the sentire cluster for us without going on to the AWS cloud.
