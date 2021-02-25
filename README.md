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
