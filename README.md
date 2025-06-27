# Using Ansible on AWS EC2

## SSH 
- Create a new EC2 Instance
    - Choose an instance type (ex. AWS Linux, Ubuntu, etc)
- Create a key pair 
    - RSA: better performance + smaller key sizes
    - Ed25519: better compatability on older systems, generally consider more secure than RSA and resistant to certain attacks

`$ chmod 400 <my-key>.pem`
- Give the key the correct permissions

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