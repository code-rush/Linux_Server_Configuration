Linux Server Configuration
==========================

Walkthrough to configure Linux Server to host a web application
---------------------------------------------------------------

### 1) Launch Virtual Machine(with your Udacity Account) and SSH into the server

1. Create new development Environment.
2. Download your private key from the given link and write down your public IP address.
If you are unable to download the private key, open the file in the browser and copy the contents of the file to a new document and save the document as `.rsa` file. You can give whatever name you wish to give to that file
3. Move the file containing the private key to `~/.ssh` folder:
`$ mv *location-of-the-.rsa-file* ~/.ssh/`
4. SSH into the server:
`$ ssh root@YOUR-PUBLIC-IP-ADDRESS -i ~/.ssh/PRIVATE-KEY-FILE`


### 2) Creating a new user and providing user the permission to sudo

1. Create new user by:
`$ adduser grader`
Here the name of the user is grader.
2. Giving new user the permission to sudo, execute the following commands:
* `$ touch /etc/sudoers.d/grader`
* `$ nano /etc/sudoers.d/grader` 
* write the following line into the file and save it.
	`grader ALL=(ALL) NOPASSWD:ALL

