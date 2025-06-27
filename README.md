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

## Installing Ansible 

`$ sudo apt update && sudo apt install ansible`

Create the inventory file
- The inventory file defines hosts that ansible is allowed to manage and how to connect to them using SSH 

Inside the inventory file add the IP addresses of the hosts
- EC2 IP addesses are ephemeral and can change after stop/starting the instance
