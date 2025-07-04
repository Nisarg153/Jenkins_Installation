PART A:  JENKIN CONFIGURATION

Pre-requisites:
•	EC2 instance is up and running
•	port 8080 is open in firewall rule
•	connect to EC2 instance using Git bash or item or any SSH terminal
Steps to install Jenkins:

Change Host Name to Jenkins
sudo hostnamectl set-hostname Jenkins
Perform update first
sudo apt update

Install Java 11
sudo apt install default-jdk -y  or sudo apt install openjdk-17-jdk -y or sudo apt install openjdk-11-jdk -y

![image](https://github.com/user-attachments/assets/1a318e83-d4f8-4b12-b9a8-065fa7a7950d)


 
 
Once install java, enter the below command

Verify Java Version
java -version

![image](https://github.com/user-attachments/assets/19c0707b-7af1-422e-ac8f-c6372b9784ce)


Maven Installation
Maven is a popular build tool used for building Java applications. Please click here to learn more about Maven. You can install Maven by executing below command:

sudo apt install maven -y

you can type mvn --version
you should see the below output.
![image](https://github.com/user-attachments/assets/b0b6cafc-0cd5-4de9-9e72-1def6f780a3c)

 

Now lets start Jenkins installation

Jenkins Setup

Add Repository key to the system
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
![image](https://github.com/user-attachments/assets/48d7ae11-06db-4e88-852f-bc58d35f616c)
 

Append debian package repo address to the system
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
![image](https://github.com/user-attachments/assets/ce5fbf23-20dd-4f1b-9062-8bfb7b236f7b)
 


Update Ubuntu package
sudo apt update
![image](https://github.com/user-attachments/assets/8407111e-714f-40c5-bdab-5c1b69e4d084)

Install Jenkins
sudo apt install jenkins -y
![image](https://github.com/user-attachments/assets/eb579ea6-b2d0-4f6c-b242-bb0b3f4a8b79)


The above screenshot should confirm that Jenkins is successfully installed.

Access Jenkins in web browser

Now Go to AWS console. Click on EC2, click on running instances link. Select the checkbox of EC2 you are installing Java and Jenkins. Click on Action. Copy the value from step 4 that says --> Connect to your instance using its Public DNS:

![image](https://github.com/user-attachments/assets/f9c52b80-119d-4324-a1c1-3722972d8267)
 
Now go to browser. enter public dns name or public IP address with port no 8080.
http://EC2_public_dns_name:8080

Unlock Jenkins
You may get screen, enter the below command in Git bash( Ubuntu console)
![image](https://github.com/user-attachments/assets/02d90f84-69e4-46dc-9420-3488e51c60de)
Get the initial password from the below file
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
![image](https://github.com/user-attachments/assets/6c54f0c2-7092-4d92-a813-f4d93ecf8faf)


Copy the password and paste in the browser.
Then click on install suggested plug-ins. 
![image](https://github.com/user-attachments/assets/a604303c-9a58-46e2-9894-31637a032e10)
 
Also create user name and password.
enter everything as admin. at least user name as admin password as admin
![image](https://github.com/user-attachments/assets/77c35ebe-e4b2-48ae-a3cc-0e127eb7bc39)

Click on Save and Finish. Click on start using Jenkins. Now you should see a screen like below:
 
![image](https://github.com/user-attachments/assets/f251c065-48a3-4985-857b-222be1c0a9f0)



PART 2:MASTER -SLAVE CONFIGURATION

![image](https://github.com/user-attachments/assets/eaea5739-842d-4d85-af26-2664bab92a65)
 


Let us learn more about Jenkins master and slave:

Jenkins Master
Your main Jenkins server is the Master. The Master’s job is to handle:
•	Scheduling build jobs.
•	Dispatching builds to the slaves for the actual execution.
•	Monitor the slaves (possibly taking them online and offline as required).
•	Recording and presenting the build results.
•	A Master instance of Jenkins can also execute build jobs directly.
Jenkins Slave
A Slave is a Java executable that runs on a remote machine. Following are the characteristics of Jenkins Slaves:
•	It hears requests from the Jenkins Master instance.
•	Slaves can run on a variety of operating systems.
•	The job of a Slave is to do as they are told to, which involves executing build jobs dispatched by the Master.
•	You can configure a project to always run on a particular Slave machine, or a particular type of Slave machine, or simply let Jenkins pick the next available Slave.
Let us see how to configure slave nodes on Ubuntu EC2. If you like to learn how to setup Jenkins Master on Ubuntu EC2 instance, click here.

Jenkins Master uses SSH keys to communicate with slave. You need to create ssh keys in Jenkins master by executing below command.

Pre-requisites:
•	Jenkins Master is already setup and running
•	Create a new EC2 instance for Slave
Steps involved:
1. Setup new EC2 instance for slave
2. Create jenkins user and Install Java, Maven in Slave node
3. Create SSH keys and upload public keys from master to slave node.
4. verify ssh connection from master to slave
5. Register slave node in Jenkins master
6. Run build jobs in Jenkins slave

Slave node configuration
(You need to create at least t2.small Ubuntu 22.0.4 instance for this slave)
only port 22 needs to be open

Change Host Name to Slave
sudo hostnamectl set-hostname Slave

Install Java

sudo apt update
sudo apt install openjdk-17-jdk -y

Install Maven
sudo apt install maven -y

Create User as Jenkins
sudo useradd -m jenkins
sudo -u jenkins mkdir /home/jenkins/.ssh


![image](https://github.com/user-attachments/assets/fcc00495-acb0-4716-a84f-cfa0a97cf7ba)




Now Login to Jenkins Master
Create SSH keys by executing below command:
ssh-keygen -t rsa -m PEM

Please over write your existing keys.

Copy SSH Keys from Master to Slave 

Execute the below command in Jenkins master EC2.
sudo cat ~/.ssh/id_rsa.pub

Copy the output of the above command:

![image](https://github.com/user-attachments/assets/19554002-d343-4053-9e0d-96bab5e91f53)


Now Login to Slave node and execute the below command
sudo -u jenkins vi /home/jenkins/.ssh/authorized_keys

This will be empty file, now copy the public keys from master into above file.

Once you pasted the public keys in the above file in Slave, come out of the file by entering :wq!


Now go into master node
ssh jenkins@slave_node_ip


![image](https://github.com/user-attachments/assets/d0f7a1d9-f155-47ef-adc2-9e47b7ab6a4f)
 




this is to make sure master is able to connect slave node. once you are successfully logged into slave, type exit to come out of slave.

![image](https://github.com/user-attachments/assets/fab84d1c-832d-4125-8357-623e374e4129)
 




Register slave node in Jenkins:
Now to go Jenkins Master, manage jenkins, manage nodes.

![image](https://github.com/user-attachments/assets/8b60c177-6ae4-4241-aeea-c887f6fc5932)

 









Click on new node. give name and check permanent agent.
give name and no of executors as 1. enter /home/jenkins as remote directory.
select launch method as Launch slaves nodes via SSH.
enter Slave node ip address as Host.

![image](https://github.com/user-attachments/assets/bc831d83-c2cf-4f0e-a433-08c8b07a8ec1)
 











click on credentials. Enter user name as jenkins. Make jenkins as lowercase as it is shown.
 Kind as SSH username with private key. enter private key of master node directly by executing below command:

![image](https://github.com/user-attachments/assets/59519b29-0b3a-4d26-b944-8d2092cc5be6)
 

sudo cat ~/.ssh/id_rsa
(Make sure you copy the whole key including the below without missing anything)
-----BEGIN RSA PRIVATE KEY-----
-----
-----END RSA PRIVATE KEY-----

click Save.
select Host key verification strategy as "manually trusted key verification strategy".

Click Save.
Click on launch agent..make sure it connects to agent node.

![image](https://github.com/user-attachments/assets/e697344e-9e80-4145-b1f4-49d1404d4114)

 


Now you can kick start building the jobs, you will see Jenkins master runs jobs in slave nodes. 

