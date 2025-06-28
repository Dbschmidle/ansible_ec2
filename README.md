# Using Ansible on AWS EC2
This is a tutorial readme for using / learning Ansible with AWS EC2 instances - this closely follows a tutorial by Jay Lacroix:

https://www.learnlinux.tv/getting-started-with-ansible-04-executing-ad-hoc-commands/


## SSH & EC2 Setup
Create a new EC2 Instance
- Choose an instance type (ex. AWS Linux, Ubuntu, etc)

Create a key pair 
- RSA: better performance + smaller key sizes
- Ed25519: better compatability on older systems, generally consider more secure than RSA and resistant to certain attacks

Give the key the correct permissions
- `$ chmod 400 <my-key>.pem`


`$ ssh -i <my-key>.pem ec2-user@<IP_ADDRESS>`
- 'ec2-user' is used for AWS Linux
- IP_ADDRESS is the public IP address for the instance

Locally, create an ssh key specific to ansible
`$ ssh-keygen -t ed25519 -C "ansible"`
- Save the key in a different file than your default ssh key
- `/home/<name>/.ssh/ansible`

Copy the ssh key to the ec2 instance
- `$ ssh-copy-id -i ~/.ssh/ansible.pub ec2-user@<SERVER_IP>` 

To view the keys in the ec2 instance:
- `cat ~/.ssh/authorized_keys`

Finally, initialize a git repo & Add your default ssh key to SSH keys on github.

## Installing Ansible & Configuration

`$ sudo apt update && sudo apt install ansible`

Create the inventory file
- The inventory file defines hosts that ansible is allowed to manage and how to connect to them using SSH 

Inside the inventory file add the IP addresses of the hosts
- EC2 IP addesses are ephemeral and can change after stop/starting the instance

Makes a conenction between all the hosts in the inventory file
- `$ ansible all --key-file ~/.ssh/ansible -i inventory -m ping `

In the inventory file add the ansible ssh private key and declare the ansible_user (ex. ec2-user)
- `ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/ansible`

OR 

create an ansible.cfg file and declare the private_key_file

To gather facts about our server(s):
- `ansible all -m gather_facts`

## Administration Commands 

Example of making a change in our server: `$ sudo apt update`
- Updating the apt cache using sudo with --become
    - `$ ansible all -m apt -a update_cache=true --become`

Upgrading dist across all servers:
- `$ ansible all -m apt -a "upgrade=dist" --become`

## Creating & Running Playbooks

To run the playbook:
- `$ ansible-playbook --ask-become-pass <PLAYBOOK_NAME>.yaml`

Create a playbook to install and start an apache2 server. 
- In AWS EC2, add an inbound rule to allow HTTP requests  

**When Conditional**

Issues can arise in playbooks when servers have different distributions (ex. package installers apt vs yum)

<sub>*install_apache.yml*</sub>
```yaml
  - name: install apache2 package
    apt:
      name: apache2
    when: ansible_distribution == "Ubuntu"
```

How to get distribution condition:
- `ansible all -m gather_facts | grep ansible_distribution`