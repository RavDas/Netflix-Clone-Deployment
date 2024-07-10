# Netflix-Clone-Deployment


Introduction:
Welcome to my blog page! In this blog, we will deploy a Netflix clone application using DevSecOps principles.

As part of this process, we will leverage Jenkins for CI/CD, Docker containers for application deployment, Kuber




### Install Java and Jenkins on the system

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

