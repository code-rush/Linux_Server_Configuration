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


### 2) Creating a new user and providing the user permissions to sudo

1. Create new user:
	`$ adduser grader`
Here the name of the user is grader.
2. Giving new user the permission to sudo, execute the following commands:
	* `$ touch /etc/sudoers.d/grader`
	* `$ nano /etc/sudoers.d/grader` 
	* write the following line in the file and save it.
		`grader ALL=(ALL) NOPASSWD:ALL


_Logout from the root and ssh with "grader"_


### 3) Updating Available Packages

_After logging with the "grader"_
1. Get the update list:
	`$ sudo apt-get update`
2. Upgrading the installed packages:
	`$ sudo apt-get upgrade`

__Getting rid of warning messages `sudo: unable to resolve host... `:
_You have to be logged in as root_
1. Execute the following command:
	`$ hostname`
2. Copy the output of the above command.
3. Open the file of hosts:
	`$ nano /etc/hosts`
4. Paste the details as shown below:
	`127.0.0.1 localhost`
	`127.0.1.1 PASTE-HERE`
	and save the file.

### 4) Generating public and private keys to allow "grader" access to the server and disabling access using password

1. Open new Terminal. Make sure you are on the local machine.
2. Generating SSH key pair:
	`$ ssh-keygen`
3. You will be asked to provide file name. Give the name of the file you like, but make sure to put that file under directory:
	`/Users/THE-USER-UNDER-WHICH-YOU-ARE-LOGGED-IN/.ssh/FILE-NAME`
4. You will be asked to give a pass-phrase.
5. Key pairs are now generated. The one that ends with .pub extension is a public key and other is private key.
6. Switch back to the terminal where you are logged in as grader on the server.
7. Make a new file 'authorized_keys' in a new directory '.ssh':
	`$ mkdir /home/grader/.ssh`
	`$ touch /home/grader/authorized_keys`
8. Copy the contents of the public key to the authorized_keys file and save the file.
9. Before disabling user access without password, make sure the keys work like expected.
	* Logout from the server.
	* ssh to the server using the key:
		`$ ssh grader@YOUR-PUBLIC-IP-ADDRESS -i ~/.ssh/YOUR-PRIVATE-KEY-FILE-NAME`
	* You will be asked to enter the pass-phrase that you used earlier while creating the keys. Enter the pass-phrase.
	* If you logged in to the server, key pair works and your identity has been added to the server.
10. Disabing password authentication for grader:
	`$ sudo nanp /etc/ssh/sshd_config`
	Change `PasswordAuthentication` from `yes` to `no`


