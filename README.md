# Lab: Setting up your environment

The purpose of this lab is to provide you with step-by-step instructions for setting up your local environment and to get you comfortable with the command line and ancillary tools that you will use constantly in this course.

**You must read all the instructions carefully before beginning and lab/assignment. Follow the instructions step-by-step. Do not jump around. This lab and almost every other lab/assignments in this course are designed to be followed in a linear manner. If you don't, then some later items may not work. Also, the screenshots may look slightly different than what you are seeing given things change.**

After completing this lab, you will be able to:

* Create your public/private ssh key pair, know where these files are located, and know how to extract their contents
* Accept a GitHub Classroom assignment/lab, how to clone it on a remote machine, and how to submit an assignment/lab
* Connect to a remote machine using ssh
* Use ssh agent forwarding to be able to work with your GitHub repositories in a remote machine
* Work with command line text editors
* Download a file using the command line
* Do some data wrangling with the command line


## Definitions

| Term(s) | Definition  |
|:-------------------|:--------------------------------------------------------|
| Terminal<br/>shell<br/>command line | Terms used interchangeably to refer to opening up a  terminal session  |
command prompt | The terminal position where you enter a command. Prompts for different systems may look different.
| __local__ machine | The machine you are interacting with (desktop, laptop) |
| __remote__ machine | The machine you connect **to** |


## WiFi (for Georgetown Students when on campus)

**If you are a Georgetown student, and you are on campus, you must be connected to _Saxanet_ wifi. Otherwise you will not be able to connect to your cloud resources or remote machines.**


## Step 1 - Terminal Setup


### Windows Users

#### PowerShell

Windows users will be using the [Windows Powershell](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/powershell). Windows Powershell is most likely installed if you have Windows 10. If you don't have Powershell, take a look at [this article](https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/) that explains how to install it.

You can find Powershell by typing "Powershell" into the search bar:

<img src='images/powershell1.png'>

Once Powershell is running, this is your terminal:

<img src='images/powershell2.png'>

#### Additional Powershell Configuration (you must do this)

You need to perform this step **only once** to be able to use agent forwarding which is explained further in the lab. 

1. Exit Powershell if running
1. Start a new Powershell session using **run as Administrator**
1. Enter the following commands, one line at a time (you can cut/paste from here):
	
	```
	Install-Module -Force OpenSSHUtils -Scope AllUsers
	Get-Service -Name ssh-agent | Set-Service -StartupType Manual
	```

1. Exit Powershell. You should not need to run as administrator going forward.

#### `git bash`

Another option for Windows users is using [git bash_](https://gitforwindows.org/) which gets installed with _git for Windows_. This shell 

### Mac and Linux Users

For Mac and Linux users, you will open up the Terminal. Macs and Linux have a built in Terminal. I prefer using [iTerm](https://www.iterm2.com/) app as another Terminal application for your Mac. I’ve been using it for years and I love it. This is not required, but truly recommended. 

In Linux, most distributions will open the terminal by using `Ctrl-Alt-T`.


## Step 2 - SSH Keypair Setup

### 2a) Create your ssh key pair

**NOTE: You only need to create your ssh public/private keypair one time only. If you already have a public/private keypair on your laptop let us know.**

1. Open the terminal for your operating system if not already open. By default, every time you open a terminal it will open in your home directory.
2. At the command prompt, type:
`ssh-keygen -t rsa -b 2048`, press enter
1. You will see this prompt, **just press enter**
```  
Generating public/private rsa key pair.
Enter file in which to save the key (/home/User/.ssh/id_rsa):
```

1. You will see this prompt, **just press enter**
```
Created directory '/home/User/.ssh'.
Enter passphrase (empty for no passphrase):
```

1. You will see this prompt, **just press enter**
```
Enter same passphrase again:
```

1. You will see these messages (your randomart will look different) and your keypair has been created.

```
Your identification has been saved in /home/User/.ssh/id_rsa.
Your public key has been saved in /home/User/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:xPJtMLmJSO73x/NQo3qMpqF6r6St4ONmshS8QZqfmHA User@WinDev1802Eval
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|       . .       |
| .  . . *        |
|+. o . = *       |
|++E o . S o o    |
|.=+o     . o .   |
|+oo o o  +o      |
|+= +.o oo.*.     |
|*+=++ooooo o.    |
+----[SHA256]-----+
``` 

### 2b) See the contents of your key files

1. Open a terminal if not already open 
1. Change to the `.ssh` directory. The `.ssh` directory is a hidden directory inside your home directory. If you list your files using `ls` you won't see it. To see all files, use `ls -la`. To change into the directory, use the `cd` command: `cd .ssh`.
1. Type `pwd` (_print working directory_) which prints your current working directory to make sure you are in the `~/.ssh` directory. 

	On a Mac, you should see something like:
	
	```
	$ pwd
	/Users/myusername/.ssh
	```
	
	If you are using Powershell in Windows you will see something like:
	
	```
	PS C:\Users\marck> pwd
	
	Path
	----
	C:\Users\marck
	
	PS C:\Users\marck>
	```
	
	If you are using Linux you'll see something like 
	
	```
	$ pwd
	/home/myusername/.ssh
	```

1. Type `ls -la` to list the files in the directory. What is displayed is my output and yours may look different. 
	* The `id_rsa` is your **private key** and this file will not leave your computer. **Treat this file as a password and don't share it with anyone.**
	* The `id_rsa.pub` file is the **public key**, whose contents we will upload to cloud services and other services so you authenticate with your pricate key 
	* The `known_hosts` is a file that gets generated as you connect to different remote systems
 	* You will not have a `config` file unless you have already created one.
 
	```
	$ ls -la
	total 32
	drwxr-xr-x   6 marck  staff   192 May 29 20:39 .
	drwxr-xr-x+ 75 marck  staff  2400 May 30 13:35 ..
	-rw-r--r--@  1 marck  staff   181 May 29 15:50 config
	-r--------@  1 marck  staff  3243 May 29 15:50 id_rsa
	-rw-r--r--@  1 marck  staff   742 May 29 15:50 id_rsa.pub
	-rw-r--r--   1 marck  staff   363 May 29 20:42 known_hosts
	```
	
1. View the contents of your **public_key** file: `cat id_rsa.pub` (what is shown is my public key, yours will be different.)

	```
	$ cat id_rsa.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCnKuIRXwZu0JZH0/Q2XNrYYTaJT7bMtXGhGQaSSOZs6MhQ4SkSbHiygO7RauQf741buLnASzY27GKMMMml6InwfxJWrF60KhNK0r869POQkuZa9v9/cmYcEIzmAJe1xRPABEZ2yfbTG9Wq4sg9cU0mwt1Bx7wiN4QNf0Bak62EC8JWTbcKLduuzO1zabIb5xW9gfR9b4K3HwmqRLl18S8bNsfYQZfvtlwd0mCWQUeuEGbDOgqh//nLIj6DeXdyxbD5xrz79iOAuAK2nXAjNCEtKpxNGQr2Py7aWQjlH+U5laDEHVg4hzmBY7yoZ5eC3Ye45yPqpQA1y8JrbXVhPJRP User@WinDev1802Eval
	```
1. Open a text editor (Notepad on Windows or Textpad on Mac, **NOT MICROSOFT WORD**) and select the output of your terminal from the `ssh-rsa` beginning all the way to the end, and paste it in your text editor as-is. We will use this in the next step. You can also just leave it here and copy/paste from your terminal screen.


## Step 3 - GitHub Setup

### 3a) Upload your Public key to GitHub

1. Create a [GitHub](http://github.com/) account if you do not already have one. **Do not proceed until this is done.**
1. Log into to your [GitHub](http://github.com/) account if you are not already logged in
1. Click on your profile icon on the top-right of the screen and select **Settings** from the dropdown
1. Click on **SSH and GPG keys** from the left hand menu
1. Click on the **New SSH key** button on the top-right
1. Give your key a name. This is just a name and is meaningful to you.
1. Paste the contents of the **public key** in the Key box, and click the **Add SSH Key** button 

### 3b) Test that your ssh key works with GitHub

1. Open a terminal if not already open on your laptop 
2. At the command prompt, type `ssh -T git@github.com` and press enter to test. If it works, you will see something like this, with your GitHub username:

```
The authenticity of host 'github.com (192.30.253.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.253.112' (RSA) to the list of known hosts.
Hi wahalulu! You've successfully authenticated, but GitHub does not provide shell access.
```

**You are now ready to use ssh authentication with GitHub.**


## Step 4 - GitHub Classroom Sample Assignment/Lab

We are using [GitHub Classroom](https://classroom.github.com/) as the platform for labs and assignments. Working with GitHub classroom is very easy. When an assignment/lab is created, we will provide you a link to the assignment in the LMS platform (Canvas, Blackboard, etc.). 

**You must have a GitHub account at this point.**

1. Check the GitHub classroom URL in the LMS. Click on it.
1. The first time you access a classroom assignment, you will be taken to the _Join the Classroom_ page. In this page, scroll through the list of names and find yours. **Make sure you click on yours and your only.** This will happen only once. You need to be logged into GitHub as well.

	<img src='images/join-the-classroom.png'>

1. Click _OK_ in the confirmation popup:
 
	<img src='images/confirmation.png'>

1. In future assignments, you will be taken directly to the _Accept the Assignment_ page. Here, click on the green button:
 
	<img src='images/accept-the-assignment.png'>

1. An assignment confirmation page will show and GitHub will begin the process of creating and a repository with the contents of the assignment. You will be notified when the creation of your repository is complete
	
	**Each student has their own repository created for every lab/assignment. The repository name usually includes your GitHub account name as part of the name of the repo. Your repository is private and must remain private. Only the intructional team and the individual student have access to each student's repo.**

1. You are now ready to visit your repository and follow the instructions.