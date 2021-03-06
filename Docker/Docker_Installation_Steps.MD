# Installing Docker on Amazon Linux server

### Pre-requisites
1. Amazon Linux EC2 Instance

## Installation Steps

1. Install docker and start docker services
   ```sh 
   yum install docker -y
   docker --version 
   
   # start docker services
   service docker start
   service docker status
   ```
2. Create a user called dockeradmin
   ```sh
   useradd dockeradmin
   passwd dockeradmin
   ```
3. add a user to docker group to manage docker 
   ```
   usermod -aG docker dockeradmin
   ```
### Validation test
1. Create a tomcat docker container by pulling a docker image from the public docker registry
   ```sh
   docker run -d --name test-tomcat-server -p 8080:8080 tomcat
   ```
   
2. Make sure you edit sshd_config file to enable ssh authentication
   ```sh
   vi /etc/ssh/sshd_config
   ```

-Update PasswordAuthentication to yes

-change PasswordAuthentication to yes or uncomment it out

-safe file and reload sshd deamon
   ```sh
   service sshd reload
   ```


#### Intergrating Docker with Jenkins
1. Open up your Jenkins console
-Navigate to:

   ```sh
   Manage Jenkins--> manage Plugins--> Available
   ```


2. search for publish over ssh plugin and install without restart

-Navigate to:

   ```sh
   manage jenkins--> configure systems--> Publish over SSH
   ```

3. select Add to add new SSH server.
-Give a name to your SSH server

-copy IP address of ya Docker server and paste in Hostname

-username is dockeradmin (docker admin user you created)

-check box to select password authentication and key in password for docker admin user.

-Test connection if successfull.

-If you receive error message;

   ```sh
   Go to Jenkins server and edit sshd_config file
   
   vi /etc/ssh/sshd_config
   ```
-Update the PasswordAuthentication section to yes.

-Test connection again and apply and safe configuration if connection is succesfull.

#### Deploy to Docker image
1. Create Dockerfile
   ```sh
   su - dockeradmin
   cd /home/dockeradmin
   vi Dockerfile
   ```
   
-Paste the following content into dockerfile and save file
   ```sh
   FROM tomcat:latest

   MAINTAINER biggclau

   COPY ./webapp.war /usr/local/tomcat/webapps
   ```
2. Edit Maven job on jenkins console  to deploy to Docker container "DockerDeploy"

   ```sh
   create new item
   select git and add URL for your repo
   select post build sactions and select "post build artifacts over SSH
   select Name "docker-host" from drop down
   source files = Webapp/target/*.war
   Remove prefix = Webapp/target
   Remote directory = .
   Exec command = "paste below code "
   cd /home/dockeradmin; docker build -t biggclau-image .; docker run -d --name biggclau-container -p 8080:8080 biggclau-image
   Apply and Save changes
   ```
3. run maven job DockerDeploy
- Validate you can access webapp from your browser.
   

## Docker Installation on CentOS server
##### Referance URL : https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository
### Pre-requisites

Please follow below steps to install docker CE on CentoOS server instance. For RedHat only Docker EE available 

1. Install the required packages.

   ```sh 
   sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
   ```
  
1. Use the following command to set up the stable repository.
 
   ```sh 
   sudo yum-config-manager \
   --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo
   ```

### INSTALLING DOCKER CE

1. Install the latest version of Docker CE.
   ```sh 
   sudo yum install docker-ce
   ```

   Note: If prompted to accept the GPG key, verify that the fingerprint matches 
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

1. Start Docker.
   ```sh 
   sudo systemctl start docker
   ```

1. Verify that docker is installed correctly by running the hello-world image.
   ```sh
   sudo docker run hello-world
   ```
