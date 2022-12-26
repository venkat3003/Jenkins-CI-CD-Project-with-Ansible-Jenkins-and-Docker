# Jenkins CI/CD Project with Ansible, Jenkins and Docker


<img width="563" alt="image" src="https://user-images.githubusercontent.com/111115490/207783257-c5c5137a-da82-4a9b-8816-f5ac03d99ae6.png">


# Step1: First we need to launch an EC2 instance with Keypair

* Named as master

<img width="294" alt="image" src="https://user-images.githubusercontent.com/111115490/207784582-403e0906-598b-499b-ae41-a683056d2fed.png">

* Select AMI as Ubuntu 20.04 and Instance Type as t2.medium

<img width="460" alt="image" src="https://user-images.githubusercontent.com/111115490/207785060-ca68a9c1-a811-41d0-a95f-61442ced0f97.png">

* We neet to lanch another 2 EC2 Instance with Keypair

* named ad Test(slave01) and Prod(slave02) with same configuration. 

<img width="512" alt="image" src="https://user-images.githubusercontent.com/111115490/207786279-e5721ce7-95ae-4fa8-84b2-1e43ede4905a.png">

# First we Login Master Node using PUTTY.

* Once our server is running, connect to your server via SSH by using your keypair.

# we need to update machine using:

sudo apt-get update

<img width="679" alt="image" src="https://user-images.githubusercontent.com/111115490/207787953-baa120a5-0f65-4f26-8955-05f64b3f94ac.png">

* we need to Access Slave Nodes thats is test and Prod so we Generate Keygen.

ssh-keygen

<img width="515" alt="image" src="https://user-images.githubusercontent.com/111115490/207789351-075f4f9b-b3c9-4225-82d5-8e5f393e206d.png">

* Here SSH key is generated:

sudo cat /home/ubuntu/.ssh/id_rsa.pub

<img width="683" alt="image" src="https://user-images.githubusercontent.com/111115490/207789806-77a27aeb-a969-45d2-92b3-06ed658e1ba7.png">

* In test Node (slave01) Paste the id_rsa.pub Key is will be our public key:

sudo nano .ssh/authorized_keys

<img width="476" alt="image" src="https://user-images.githubusercontent.com/111115490/207791006-1206dd59-3e51-4e5e-af30-773f64fb68d6.png">

# Install Ansible in Master Machine:

$ sudo apt update

$ sudo apt install software-properties-common

$ sudo add-apt-repository --yes --update ppa:ansible/ansible

$ sudo apt install ansible

<img width="517" alt="image" src="https://user-images.githubusercontent.com/111115490/207792170-58f4eb32-aa0c-4dd0-8f2e-a8f33037c9a3.png">

# to go Inside Ansible:

cd /etc/ansible

<img width="292" alt="image" src="https://user-images.githubusercontent.com/111115490/207792928-cf856c6a-8464-42b2-81f8-9d480f67ca25.png">

# Script Using YAML playbook in Ansible:

sudo nano install.yml

<img width="401" alt="image" src="https://user-images.githubusercontent.com/111115490/207795817-b444dd4f-ff81-4554-9dad-d988b4959985.png">

* to Go inside hosts file:

sudo nano hosts

<img width="407" alt="image" src="https://user-images.githubusercontent.com/111115490/207799643-6ccf282d-765e-48d6-9252-ab7e9070df2f.png">


* Next we need to add Ansible server to our /etc/ansible/hosts file.

[test]

172.31.94.195

[prod]

172.31.95.172

<img width="205" alt="image" src="https://user-images.githubusercontent.com/111115490/207797942-2ee40469-1ff7-4715-91d1-2cf70ee1903f.png">

* to check successfully connected Ping hosts

ansible -m ping all


<img width="574" alt="image" src="https://user-images.githubusercontent.com/111115490/207800121-c83aa66a-861d-48e6-8623-6e0e00891149.png">

# Jenkins Scripts:

sudo nano jenkins.sh


curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
  
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
 
 
sudo apt-get update
  
sudo apt-get install docker.io -y

sudo apt-get install openjdk-11-jdk -y 

sudo apt-get install jenkins -y
 
<img width="517" alt="image" src="https://user-images.githubusercontent.com/111115490/207804081-6421ee2f-3be7-4633-9ba7-3758459382ca.png">

# docker Scripts for test and Prod machines 

sudo nano docker.sh

sudo apt-get update

sudo apt-get install docker.io -y

sudo apt-get install openjdk-11-jdk -y 


<img width="392" alt="image" src="https://user-images.githubusercontent.com/111115490/207804727-9a1e843c-b6e4-48d9-89b0-745cf2c4c0e6.png">

* Now we are ready to run our playbook:

ansible-playbook install.yml

<img width="680" alt="image" src="https://user-images.githubusercontent.com/111115490/207806302-802e0785-de7c-4825-ba0c-a94afe051771.png">


* Now its successfully Installed: 


<img width="680" alt="image" src="https://user-images.githubusercontent.com/111115490/207806560-9b9650ef-1ea3-400b-bb63-740c429f82d9.png">


# Configure Jenkins Server: (STEP:1 )

* Now Go and copy of your Master's EC2 Instance Public IP Address

* Connect to http://<your_server_public_DNS>:8080 from your browser. You can get initialAdminPassword for jenkins by running below command:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

<img width="607" alt="image" src="https://user-images.githubusercontent.com/111115490/207807778-e2b5cad6-9c9d-48d8-b9f9-8cc9ec3e7a97.png">


# Integrate Git with Jenkins:

* Now we need to clone github repo by below command:

git clone https://github.com/guru716/website.git

* Now ls you can see github files 


<img width="581" alt="image" src="https://user-images.githubusercontent.com/111115490/207809975-589ec098-286f-4e89-b365-6d5f513a05b5.png">



# Create Customized Dockerfile:

* to create Dockerfile

sudo nano Dockerfile

FROM ubuntu
RUN apt-get update
RUN apt-get -y install apache2
ADD . /var/www/html
ENTRYPOINT apachectl -D FOREGROUND

<img width="386" alt="image" src="https://user-images.githubusercontent.com/111115490/207811528-708d2cfd-7766-449e-82c0-d84a0dd0d507.png">

* Now we push Dockerfile to github repo by bellow Steps:

git status

<img width="535" alt="image" src="https://user-images.githubusercontent.com/111115490/207812047-f87b9246-339f-45d4-b123-a43d8127e209.png">

git add Dockerfile

* Now we commit the Dockerfile by bellow command 

git commit -m "adding dockerfile"

<img width="495" alt="image" src="https://user-images.githubusercontent.com/111115490/207812843-d644cfa1-4db5-475c-a209-60e14d6ce4d7.png">

* Next we need to create Develop branch

git branch Develop

git checkout Develop

<img width="383" alt="image" src="https://user-images.githubusercontent.com/111115490/207814060-fb35be31-e312-481f-9136-29a75b5e6e60.png">

git push origin develop

# Attach test and Prod in Jenkins server: (STEP:2 )

* Go to Jenkins server, Manage jenkins -> Manage nodes and clouds. Click New Node 

Enter Node name (test), select Permanent Agent, click create.

<img width="463" alt="image" src="https://user-images.githubusercontent.com/111115490/207818538-0645c899-8823-4391-adac-b6cfeac26eaf.png">


* Inside Node Monitoring -> Root directory as /home/ubuntu/jenkins/ -> select launch Method as Launch agents via ssh -> hosts will be your 
Private IP of You test node (slave01)

<img width="589" alt="image" src="https://user-images.githubusercontent.com/111115490/207822129-a3219414-5e0d-4e90-9dd9-ca1c0b18d5d5.png">


* Credentials -> username: Ubuntu, Enter private Key of Your test node(slave01) EC2 instance.


<img width="588" alt="image" src="https://user-images.githubusercontent.com/111115490/207820570-ed93ca2c-edb2-4080-919e-cd1e7302d9df.png">


* Test Node will be successfully added and its running


<img width="593" alt="image" src="https://user-images.githubusercontent.com/111115490/207822629-4bb697a5-15f4-4657-8f37-42ec3de731f6.png">


* Add another node its Prod (slave02) 

Enter Node name (prod), select Permanent Agent, click create.

<img width="522" alt="image" src="https://user-images.githubusercontent.com/111115490/207823096-f16c0a73-67f2-4aab-aadd-fccde8d39c3b.png">


*  Inside Node Monitoring -> Root directory as /home/ubuntu/jenkins/ -> select launch Method as Launch agents via ssh -> hosts will be your 
Private IP of You Prod node (slave02)
  
*Credentials -> username: Ubuntu, Enter private pem Key of Your  node(slave02) EC2 instance.

* Now Test(slave01) and Prod(slave02) nodes are successfully running in Jenkins.

<img width="602" alt="image" src="https://user-images.githubusercontent.com/111115490/207827468-ccc8717f-0bb4-4063-b5f0-a9e238039152.png">


# Creating jobs In Jenkins:

* Go to Dashboard click on New item -> Enter Name as Job1 -> Select Freestyle project, Click OK

<img width="575" alt="image" src="https://user-images.githubusercontent.com/111115490/207828979-1fd9c327-5e98-465c-8b28-7a6268ba0578.png">

* In General -> Description as Testing From Develop

  Branch -> Select github Project (Paste your Project URL) 

* Select Restrict where the project can be run. In Lable Expression (test Node)

<img width="545" alt="image" src="https://user-images.githubusercontent.com/111115490/207830400-69a9ed8a-4c68-4c57-ba85-5c394ffa6f41.png">

* Source code Management: Select Git -> Repository URL (paste your develop branch github URL) 

<img width="530" alt="image" src="https://user-images.githubusercontent.com/111115490/207830957-ded04921-157a-41c8-84d5-24765d33fa1f.png">

* Branches to build: Specifi your branch as Develop

<img width="512" alt="image" src="https://user-images.githubusercontent.com/111115490/207831925-c32ff83d-9b24-4c19-b4a5-a3795cd2c48c.png">

* Next Build triggers: Select github hook trigger for GITScm polling. ( Whenever changes will come Job will automatically Run)

<img width="263" alt="image" src="https://user-images.githubusercontent.com/111115490/207832855-e8b32a31-9480-4902-a3bc-bc5095f3f4f5.png">


 * At the Same time we need to update Jenkins URL in GitHub repositorie
 
 > https://URL:8080/github-webhook/ -> click Add webhook
 
 <img width="630" alt="image" src="https://user-images.githubusercontent.com/111115490/207858552-6f9bb80b-3d5c-4b79-b241-3a4dff448fa6.png">
 
 * If You click Build Now. Job1 successfully Run.
 
 <img width="251" alt="image" src="https://user-images.githubusercontent.com/111115490/207859819-c7f9fc45-7ef2-4060-8614-b86b5f34523b.png">

# configure the Docker container in Job1

* Go to Job1 -> Click Configure 

In configure -> Build -> Select Execute shell 

* Execute Command for Docker:

sudo docker build /home/ubuntu/jenkins/workspace/job1 -t developapp

<img width="524" alt="image" src="https://user-images.githubusercontent.com/111115490/207861797-90fc3a1a-d98b-481d-aba6-bbb0588ad569.png">

* to run docker container from this image.

sudo docker run -itd --name c1 -p 81:80 developapp

* Now Click Build Now (Build successfully)

<img width="242" alt="image" src="https://user-images.githubusercontent.com/111115490/207862503-87e0edab-6077-4552-9cc8-081d5ccc9ce1.png">

* Copy the test node (slave01) Instance's Public IP address and add Port :81  

* Now particular (test node) web server is Running Successfully.

<img width="521" alt="image" src="https://user-images.githubusercontent.com/111115490/207864648-00de09a0-99be-43c5-95fb-65060f724407.png">

* If you run it once again You may Face Error

<img width="575" alt="image" src="https://user-images.githubusercontent.com/111115490/207868186-f7989439-4f21-44de-9838-51d732a8313d.png">

* C1 port is already there You are Using same Port. we have Go in the configuration and Add One Command so when ever there will be a container
some new Image will be Generated or In Github. previous Container will be Deleted and new one will created By using Bellow Command.

sudo docker rm -f $(sudo docker ps -a -q) 

* Job1 Has be Done.

# Now we Push Dockerfile to Master Branch

git push origin master

* Enter the Github username:
* Enter the Github Password:

# creating Another Job2 In Master:

* Go to Dashboard click on New item -> Enter Name as Job2 -> Select Freestyle project, Click OK

* Description: From master machine testing

* Select Restrict where the project can be run. In Lable Expression (test Node)

* Source code Management: Select Git -> Repository URL (paste your Master branch github URL)

* Branches to build: Specifi your branch as Master

<img width="529" alt="image" src="https://user-images.githubusercontent.com/111115490/207874236-9d530ecc-51a2-48c0-b688-b58085c093fd.png">

* Next Build triggers: Select github hook trigger for GITScm polling. ( Whenever changes will come Job will automatically Run)

* Click Apply and Save 

# Before Running Job2 in test machine only job1 is avaliable

<img width="333" alt="image" src="https://user-images.githubusercontent.com/111115490/207875307-83af635f-a69a-4237-8c6c-0b844975195a.png">

* After (Build Now) running the job2 

<img width="337" alt="image" src="https://user-images.githubusercontent.com/111115490/207875889-f8e0cccc-d505-4fa7-8c4e-53e3369e9569.png">

* You can see job1,job2 is avaliable In test machine.

<img width="323" alt="image" src="https://user-images.githubusercontent.com/111115490/207876167-f8c58169-9635-4784-b85f-30f2bb6cf541.png">


# configure the Docker container in Job2

* Go to Job2 -> Click Configure 

* Execute Command for Docker:

sudo docker build /home/ubuntu/jenkins/workspace/job2 -t masterapp

sudo docker run -itd -p 82:80 masterapp

* Removing the Previous Container

sudo docker rm -f $(sudo docker ps -a -q)

<img width="509" alt="image" src="https://user-images.githubusercontent.com/111115490/207881934-5ccbbdf2-c7e6-4c1f-ba7a-27b35ec0b5ce.png">

* Now Successfully add and Running Job1 and Job2 Nodes.

<img width="627" alt="image" src="https://user-images.githubusercontent.com/111115490/207882345-3fb3bbdc-e344-43e6-95ba-548abee31e05.png">


# Now Put In Production Environment:

* Create a new Job -> Job3
* Description -> Putting the Web App In Production Environment

<img width="602" alt="image" src="https://user-images.githubusercontent.com/111115490/208035634-186c68e5-0ec9-4e9d-8a01-788b63cfbdea.png">

* then Restrict this project can be run (selected)

* Lable Expression Run In Production

<img width="547" alt="image" src="https://user-images.githubusercontent.com/111115490/208036365-28ae350a-eee7-49c3-ad77-01a441f847c0.png">

* Source code Management Should be In Git ( paste the Master Branch Github URL)

* Now In Build Execute Command for Dockerfile:

sudo docker build -t finalrelease 

sudo docker run -itd -p 80:80 finalrelease

<img width="453" alt="image" src="https://user-images.githubusercontent.com/111115490/208039126-537207cd-d1e0-453a-bd87-63ac8dfa608f.png">


# At the same Time Go Inside Job2 -> Choose Post-Build Actions -> Build Other Project Choose as Job3 -> Select trigger only if build is stable 

<img width="536" alt="image" src="https://user-images.githubusercontent.com/111115490/208037678-40c01197-aa62-4740-bde1-d92f473e18f7.png">

* Now Job2 will trigger Automatically Job3 will be start Running


<img width="632" alt="image" src="https://user-images.githubusercontent.com/111115490/208039823-3d21caef-09f9-4a64-bbea-4f79ea615c5e.png">


* Job3 it Has be Successfully Run, Next Copy the Production (slave3) instance Public IP address and Paste It in Google. It Automatically take Port 80


<img width="437" alt="image" src="https://user-images.githubusercontent.com/111115490/208039597-2eac50ce-6b36-4fac-901f-511221f65c4f.png">


# Finally Server is Running Successfully


<img width="470" alt="image" src="https://user-images.githubusercontent.com/111115490/208040763-a4d67d17-9782-4f4f-91eb-449d24bef47c.png">


 
