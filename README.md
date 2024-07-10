# Netflix-Clone-Deployment


Introduction:
Welcome to my blog page! In this blog, we will deploy a Netflix clone application using DevSecOps principles.

As part of this process, we will leverage Jenkins for CI/CD, Docker containers for application deployment, Kuber




### Install Java, Jenkins, Docker and Trivy

#### Install Jenkins

```
#!/bin/bash
sudo apt update -y
#sudo apt upgrade -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
                  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
                  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
                              /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins
```

#$#%$%#$%$

Once Jenkins is installed, you will need to go to your AWS EC2 Security Group and open Inbound Port 8080, since Jenkins works on Port 8080.

Now, grab your Public IP Address - <EC2 Public IP Address:8080>


```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Unlock Jenkins using an administrative password and install the suggested plugins.

![1](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ca2bad8c-dc7c-4761-ba2b-8447246e002b)

Jenkins will now get installed and install all the libraries.

![2](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/b636ae2f-64ba-4088-bf6c-6a8c6c8d4170)

Create a user click on save and continue.

![3](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/3230a162-f517-4d9b-bc11-db7f1ece28e0)

Jenkins Getting Started Screen.

![4](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/87aeeed7-80bb-4028-8ac0-c15447db0d79)


### Installation of Docker on the VM

```
# 1. Update the package list and install necessary packages
sudo apt-get update 
sudo apt-get install ca-certificates curl

# 2. Download and add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings 
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc 
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 3. Add Docker repository to Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Update package index
sudo apt-get update

# 5. Install Docker packages
sudo apt-get install docker-ce docker-ce-cli containerd.io -y

# 6. Add user to Docker group
sudo usermod -aG docker $USER

# 7. Apply group changes (log out and back in or use the following command)
newgrp docker

# 8. Grant permission to Docker socket (optional, for convenience)
sudo chmod 666 /var/run/docker.sock
```

Following these steps, you should have successfully installed Docker on your Ubuntu system. You can now start using Docker to containerize and manage your applications.

Follow this official document if you find any errors: Link: https://docs.docker.com/engine/install/ubuntu/


After the docker installation, we create a sonarqube container (Remember to add 9000 ports in the security group).

-run -> docker run -d –name sonar -p 9000:9000 sonarqube:lts-comminity

![5](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6aae6964-12db-475d-8f58-39e3369d00a2)

-access using <public_ip:9000>

Now our sonarqube is up and running,

username: admin and password:admin

![6](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/3742eb3d-ed0c-457d-ac2e-4b721b5c9c01)

Enter username and password, click on login and change password

![7](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/bb900408-1f6a-42e0-9635-3ec67d6fc6da)

Update New password, This is Sonar Dashboard.

![8](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6171a1fa-bfc4-4e31-9d11-c4054bb396de)

### Install Trivy

```
vi trivy.sh
```
```
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```

### Create a TMDB API Key

Next, we will create a TMDB API key

Open a new tab in the Browser and search for TMDB

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/1abc2533-5cee-4748-88d5-9b2a061cd053)

Click on the first result, you will see this page

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ea339326-3b8a-4f69-a89d-8de1da8147a1)


Click on the Login on the top right. You will get this page.

You need to create an account here. click on click here. I have account that’s why i added my details there.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/9150828f-6618-497e-9af9-a354787a4284)

Once you create an account you will see this page.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/a1c1a9da-ec37-4f16-baad-824897c1c5d5)


Let’s create an API key, By clicking on your profile and clicking settings.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/fd3264c0-318a-4ce1-8c98-84cb96289394)

Now click on API from the left side panel.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/8356949a-f106-40cc-a813-b070dce141ed)

Now click on create

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/bf607a17-e622-4d50-96cd-1efaa504f8d8)


Click on Developer

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ab79f1ae-eed6-449d-9486-8a1a7419b9ff)

Now you have to accept the terms and conditions.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/70d114d3-ea21-4755-9273-d6bcd4769157)

Provide basic details

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/fd2b9b3d-081d-4802-aa4d-0c820ab59027)

Click on submit and you will get your API key.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f8ac5701-419f-44b4-b277-e6e95c0069f9)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/9e2033d9-a7a7-4539-8eea-9767889bba20)


