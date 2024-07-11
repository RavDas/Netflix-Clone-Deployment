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

![9](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ee830bbc-18be-41c0-b4f5-5f8b95f04d6a)

Once you create an account you will see this page.

![10](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/aba73221-cfda-47c7-94c2-e4a88bf21426)

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


### Install Prometheus and Grafana on the new Server (Second EC2 instance)

Create a dedicated Linux user sometimes called a system account for Prometheus. Having individual users for each service serves two main purposes:

* It is a security measure to reduce the impact in case of an incident with the service.

* It simplifies administration as it becomes easier to track down what resources belong to which service.

To create a system user or system account, run the following command:

```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false prometheus
```
* ``` --system ``` – Creates a system account.
* ``` --no-create-home ``` – No home directory is needed for Prometheus or any other system accounts in our case.
* ``` --shell /bin/false ``` – Prevents logging in as the Prometheus user.
* ``` prometheus ``` – Creates a Prometheus user and a group with the same name.
* 
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/5a547d2d-90b8-4aae-a64f-4a1f0c92ac84)

Let’s check the latest version of Prometheus from the download page.

You can use the curl or wget command to download Prometheus.

```
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/8613e067-8992-4075-9db7-680bff441516)

Extract all Prometheus files from the archive.

```
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/65560b7d-949c-4aea-9aff-866b90bb0d8e)

in general a disk is mounted to the data directory. For now, I will simply create a /data directory. Also, you need a folder for Prometheus configuration files.

```
sudo mkdir -p /data /etc/prometheus
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f6815436-ede9-4ac5-8469-3390e4a62e27)

Then change the directory to Prometheus and move some files.

```
cd prometheus-2.47.1.linux-amd64/
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/0b24393f-e3de-40da-ac1d-41195d4be901)

First of all, let’s move the Prometheus binary and a promtool to the /usr/local/bin/. 
Promtool is used to check configuration files and Prometheus rules.

```
sudo mv prometheus promtool /usr/local/bin/

```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/05179f02-6b9e-49f0-8401-d24d077ffccd)

To avoid permission issues, you need to set the correct ownership for the /etc/prometheus/ and data directory.

```
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2e3b781a-6aae-4902-96ca-f9d40d13230e)


You can delete the archive and a Prometheus folder when you are done.

```
cd
rm -rf prometheus-2.47.1.linux-amd64.tar.gz
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/8a7f9324-5f9b-4605-a91b-047d6135063f)


Verify that you can execute the Prometheus binary by running the following command:

```
prometheus --version
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6a0004fe-5210-4c18-a561-cefb8bf9c5b3)


To get more information and configuration options, run Prometheus Help.

```
prometheus --help
```


We’re going to use systemd, which is a system and service manager for Linux operating systems. For that, we need to create a Systemd unit configuration file.

```
sudo vim /etc/systemd/system/prometheus.service
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/249825e9-1de6-49c3-8ed1-93d3b2ff0f40)


Prometheus.service

```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/e1760c72-bd55-422e-8f63-ed6b5f2fb20a)


To ensure Prometheus starts automatically after reboot, you can enable it in systemd.

Here’s a summary of the important options related to Systemd and Prometheus configuration:

Restart: Configures whether the service shall be restarted when the service process exits, is killed, or a timeout is reached.

RestartSec: Configures the time to sleep before restarting a service.

User and Group: Specifies the Linux user and group to start the Prometheus process.

— config.file=/etc/prometheus/prometheus.yml: Path to the main Prometheus configuration file.
— storage.tsdb.path=/data: Location to store Prometheus data.
— web.listen-address=0.0.0.0:9090: Configures Prometheus to listen on all network interfaces. If you have a proxy like nginx handling external requests, you might restrict Prometheus to localhost.
— web.enable-lifecycle: Allows managing Prometheus, such as reloading configuration without restarting the service.

To automatically start the Prometheus after reboot, run enable.

```
sudo systemctl enable prometheus
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4e86c1f2-23a9-410f-b295-862d66caf458)

Then just start the Prometheus.

```
sudo systemctl start prometheus
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/3e4e8840-fb88-4e47-9d07-4c5ab4f6a03f)


To check the status of Prometheus run the following command:

```
sudo systemctl status prometheus
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4242be60-9e63-49c6-a982-9299a14da0e0)

Suppose you encounter any issues with Prometheus or are unable to start it. The easiest way to find the problem is to use the ```journalctl``` command and search for errors.

```
journalctl -u prometheus -f --no-pager
```

Now we can try to access it via the browser. I’m going to be using the IP address of the Ubuntu server. You need to append port 9090 to the IP.

```
<public-ip:9090>
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/b95b50a7-eaaf-47e5-9161-7320b22d4acd)

If you go to targets, you should see only one – Prometheus target. It scrapes itself every 15 seconds by default.

### Install Node Exporter on Ubuntu 22.04

Next, we’re going to set up and configure Node Exporter to collect Linux system metrics like CPU load and disk I/O. Node Exporter will gathers system metrics and exposes them in a format which can be ingested by Prometheus. Since the installation process is very similar, I’m not going to cover as deep as Prometheus.

First, let’s create a system user for Node Exporter by running the following command:

```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false node_exporter
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/51ad9564-326b-478a-8fd4-7a924f59fad8)


Use the wget command to download the binary.

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/d90c40d2-a3af-4eb4-af9a-bf21edd2fe5c)

Extract the node exporter from the archive.

```
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ceba76c9-bfc6-43c9-9310-bc7b4a750782)

Move binary to the /usr/local/bin.

```
sudo mv \
  node_exporter-1.6.1.linux-amd64/node_exporter \
  /usr/local/bin/
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/68e2150a-7556-4c51-9aa7-c2b9df2b24bc)

Clean up, and delete node_exporter archive and a folder.

```
rm -rf node_exporter*
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f535a341-6059-4d53-8480-b38cb7327caa)

Verify that you can run the binary.

```
node_exporter --version
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ee89e3f8-e38b-436c-9323-dce413dbd23f)

Node Exporter has a lot of plugins that we can enable. If you run Node Exporter help you will get all the options.

```
node_exporter --help
```

–collector.logind We’re going to enable the login controller, just for the demo.

Next, create a similar systemd unit file.

```
sudo vim /etc/systemd/system/node_exporter.service
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/0ffa3644-9910-4877-abed-7eae120201e8)


node_exporter.service

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter \
    --collector.logind

[Install]
WantedBy=multi-user.target
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/a500c8ce-128b-4d98-9a0b-c83e615f2058)

Replace Prometheus user and group to node_exporter, and update the ExecStart command.

To automatically start the Node Exporter after reboot, enable the service.

```
sudo systemctl enable node_exporter
```

Then start the Node Exporter.

```
sudo systemctl start node_exporter
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/708aa767-d721-4d93-b6d7-9bdacfeeddc1)

Check the status of Node Exporter with the following command:

```
sudo systemctl status node_exporter
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ec45189e-06be-42be-9610-eb3a97c5d11d)

If you have any issues, check logs with journalctl

```
journalctl -u node_exporter -f --no-pager
```

At this point, we have only a single target in our Prometheus. There are many different service discovery mechanisms built into Prometheus. For example, Prometheus can dynamically discover targets in AWS, GCP, and other clouds based on the labels. For this tutorial, let’s keep it simple and keep adding static targets. Also, I have a lesson on how to deploy and manage Prometheus in the Kubernetes cluster.

To create a static target, you need to add job_name with static_configs.

```
sudo vim /etc/prometheus/prometheus.yml
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c4488049-c4e7-4f44-8cc0-5acc6b7fac20)

prometheus.yml

```
  - job_name: node_export
    static_configs:
      - targets: ["localhost:9100"]
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c03c49a3-e519-44c0-b87b-a82f942ddf36)

By default, Node Exporter will be exposed on port 9100. We use localhost since Prometheus and Node Expoter are in the same EC2 instance.

Since we enabled lifecycle management via API calls, we can reload the Prometheus config without restarting the service and causing downtime.

Before restarting, check if the recent configuration is valid.

```
promtool check config /etc/prometheus/prometheus.yml
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ecf359b9-a1bf-47f0-9f5c-0b51420d13a0)


Then, you can use a POST request to reload the config for Node Exporter.

```
curl -X POST http://localhost:9090/-/reload
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/0bb95d2e-c5ac-4b40-b9e2-4aed4ae62017)

Check the targets section

```
http://<ip>:9090/targets
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/eb6df0d0-245b-4414-9323-dc4d8f6307dd)


### Install Grafana on Ubuntu 22.04

To visualize metrics we can use Grafana. There are many different data sources that Grafana supports, one of them is Prometheus.

First, let’s make sure that all the dependencies are installed.

```
sudo apt-get install -y apt-transport-https software-properties-common
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/a79dfcac-bc65-464e-bc62-818ade147c3c)


Next, add the GPG key.

```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6f8357e9-8d24-4f51-ae5f-26b774a1b96d)


Add this repository for stable releases.

```
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/59406c0c-6715-4a5a-aecf-e6fab5cb8e9f)

After you add the repository, update and install Garafana.

```
sudo apt-get update

sudo apt-get -y install grafana
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/eee86dbe-37e1-44c8-95a1-d265d7d39409)

To automatically start the Grafana after reboot, enable the service.

```
sudo systemctl enable grafana-server
```

Then start the Grafana.

```
sudo systemctl start grafana-server
```
![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2290e32c-89c2-49bc-8e3f-eca01117656d)

To check the status of Grafana, run the following command:

```
sudo systemctl status grafana-server
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/b2e6062d-667e-4382-b699-88a1cc4dfd34)


Go to http://<ip>:3000 and log in to the Grafana using default credentials. The username is admin, and the password is admin as well.

```
username admin
password admin
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ee8d2205-e890-4c53-a839-9c2377e1b806)

When you log in for the first time, you get the option to change the password.

<div style="overflow: hidden; height: 200px;">
  <img src="https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/741be3d0-bebd-423b-8017-6b1c8deb8b9a" style="margin-top: -500px;">
</div>

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/741be3d0-bebd-423b-8017-6b1c8deb8b9a)


To visualize metrics, you need to add a data source first.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/9be252d7-ad31-4f9a-a964-57bb1f6aa529)


Click Add data source and select Prometheus.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/08d1c388-a1f5-4754-8a1e-e05394dd13a5)


For the URL, enter localhost:9090 and click Save and test. You can see Data source is working.

```
<public-ip:9090>
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ee747f05-58a9-4104-9369-4a87f4f2ddf6)

Click on Save and Test.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/72a9e20e-4890-421b-867b-3dd6bbe67c9d)

Let’s add Dashboard for a better view

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f063332b-2b67-4dc0-8534-371ef3c74601)

Click on Import Dashboard paste this code 1860 and click on load

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2fa78730-8747-42f0-a918-690601d1c3b7)


Select the Datasource and click on Import

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/a6b1c24f-076c-428c-9f39-8fe7866b7dcf)

You will see this output

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/d441968d-f4ec-46a7-829f-edc1d10b0590)


### Install the Prometheus Plugin and Integrate it with the Prometheus server

Let’s monitor our Jenkins

Need Jenkins up and running machine

Goto Manage Jenkins –> Plugins –> Available Plugins

Search for Prometheus and install it

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2e6f7d58-e8ff-4247-b3cb-d8dab71a4e86)


Once that is done you will Prometheus is set to /Prometheus path in system configurations

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/5f8685a9-515c-461f-8f18-96ea88265d11)


Nothing to change click on apply and save

To create a static target, you need to add job_name with static_configs. go to Prometheus server

```
sudo vim /etc/prometheus/prometheus.yml
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/55fcfa05-b935-4ab5-a012-e1972a5cfcf8)

Paste below code

```
  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<jenkins-ip>:8080']
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4483863e-732d-4319-881a-f9bd7dea5e35)

Before, restarting check if the config is valid.

```
promtool check config /etc/prometheus/prometheus.yml
```

Then, you can use a POST request to reload the config.

```
curl -X POST http://localhost:9090/-/reload
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ab99ee23-3f65-4747-9912-f49726bb3627)

Check the targets section

```
http://<ip>:9090/targets
```

You will see Jenkins is added to it

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/bef7dd36-4b49-4855-a91d-2b00ec8752c5)


Let’s add Dashboard for a better view in Grafana

Click On Dashboard –> + symbol –> Import Dashboard

Use Id 9964 and click on load

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/759d06d9-9ca8-40f5-9daa-4e4074a33599)

Select the data source and click on Import

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6352e07a-7847-4056-9b3d-e7f4b9f84f16)

Now you will see the Detailed overview of Jenkins

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/7c6463d3-c63b-497f-a751-c02a835d2efc)


### Email Integration With Jenkins and Plugin Setup

Install Email Extension Plugin in Jenkins

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2413c73b-975c-4f23-bc2d-16c4c46d32cc)


Go to your Gmail and click on your profile

Then click on Manage Your Google Account –> click on the security tab on the left side panel you will get this page(provide mail password).

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/7b6d0b22-0223-4e57-868c-84ff4024bc32)

2-step verification should be enabled.

Search for the app in the search bar you will get app passwords like the below image

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ade10600-a3fb-46f4-9e38-81b35451b8e0)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4b3694b5-63f3-426c-9cfa-05c6e40db14b)


Click on Generate and copy the password

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/197cfa2a-b104-4e0b-bd57-c389cafda4f0)


Once the plugin is installed in Jenkins, click on manage Jenkins –> configure system there under the E-mail Notification section configure the details as shown in the below image

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ba8c11b6-a3c9-4b75-8d25-adbe764701db)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c978f075-7633-4b72-8c11-46ef38a76e18)

Click on Apply and save.

Click on Manage Jenkins–> credentials and add your mail username and generated password

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ac99e043-ecde-4dcb-a213-b2a94f85f14d)


This is to just verify the mail configuration

Now under the Extended E-mail Notification section configure the details as shown in the below images

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/724bf07f-da8c-4902-971c-e87ee1c984a2)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/04e3b892-1fc0-4933-87d4-3204a5224102)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ebb5a10a-8710-40c1-809e-3d6873a3d162)

Click on Apply and save.

```
post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'postbox.aj99@gmail.com',  #change Your mail
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
```

Next, we will log in to Jenkins and start to configure our Pipeline in Jenkins

### Install Plugins like JDK, Sonarqube Scanner, NodeJs, OWASP Dependency Check

#### Install Plugins

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 → Eclipse Temurin Installer (Install without restart)

2 → SonarQube Scanner (Install without restart)

3 → NodeJs Plugin (Install Without restart)


![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/5d1e0de0-2c21-4a88-8ccb-9b459a3b51a4)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4a46ceca-5c20-45ff-9aa6-23ed44a3595e)


#### Configure Java and Nodejs in Global Tool Configuration

Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c4019d57-d385-46bb-b2c3-19e5500ffad5)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/1a5247ed-dece-4845-b43c-55379402a177)


#### Create a Job

Create a job named Netflix, select pipeline and click on ok.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2008a011-fee9-4862-a610-d10b63de3357)

### Configure Sonar Server in Manage Jenkins

Get the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, so ```<Public_ip>:9000```. 

Goto your Sonarqube Server. 

Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/e5bb8c1b-a954-42d2-a5de-f1134264259b)

Click on update Token

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ca1d5ccf-8144-45f3-a4d3-1ec112303081)


Create a token with a name and generate

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/b95c0073-845d-4e9a-9068-cdeadb1b8b30)


Copy the token

Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/1b45ab9d-21d2-4570-bd8a-9a11d355f6e7)


You will this page once you click on create

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/419c0500-bb53-45e0-952d-bf142cd4db95)


Now, go to Dashboard → Manage Jenkins → System and Add like the below image.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4d11754d-4466-4db5-acd7-bfe668b8bcb8)


Click on Apply and Save

**Configure System** option is used in Jenkins to **configure different servers**.

**Global Tool Configuration** is used to configure **different tools** that we install using plugins

We will install a sonar scanner in the tools.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/40de6f71-a810-498f-8c9b-69f64ea0b42c)


In the Sonarqube Dashboard add a quality gate also

Administration–> Configuration–>Webhooks

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/9a09c6bc-4901-436f-a247-c9428a6e192b)

Click on Create

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f3286e1f-ff5a-41ce-be15-6b2b93ba14e5)


Add details

```
#in url section of quality gate
<http://jenkins-public-ip:8080>/sonarqube-webhook/
```

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c293e8ab-bb25-4811-af19-7cbd4d6fbafb)



Now go to pipeline section in Jenkins and add below script to build the pipeline.

```
pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/Aj7Ay/Netflix-clone.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            } 
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'postbox.aj99@gmail.com',
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
}
```

Email notification successfully received on personal Gmail account.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/2c3fc4be-d1cb-4215-a5ef-ae0af1a2ce48)

Click on Build now, you will see the stage view like this

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/55070413-bbff-42ec-a0c9-1ccd81bb6e8d)


To see the report, you can go to Sonarqube Server and go to Projects.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/7cd8215b-844f-4902-9e1b-3fd9481b4275)

You can see the report has been generated and the status shows as passed. You can see that there are 3.2k lines it scanned. To see a detailed report, you can go to issues.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/72e4e17a-38ad-4146-9248-06c297b0a95f)



### Install OWASP Dependency Check Plugins

GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install it without restart.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/7559db6b-d010-4137-b099-a8eba26bf96b)

First, we configured the Plugin and next, we had to configure the Tool

Go to Dashboard → Manage Jenkins → Tools 

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/90e84449-733a-4554-a19d-633b0ea22ab5)


Click on Apply and Save here.

Now go Jenkins configure → Pipeline and add this stage to your pipeline and build.

```
stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
```

The stage view would look like this,

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/cfe6cb9e-9537-47f4-ab14-20f1c5c6f92d)

You will see that in status, a graph will also be generated and Vulnerabilities.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/5452fff1-34f3-4a6a-bafd-9fb0169b7bff)


### Docker Image Build and Push

We need to install the Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins

* Docker

* Docker Commons

* Docker Pipeline

* Docker API

* docker-build-step

and click on install without restart

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/bd527844-5f6d-4f0f-8b5e-c44e55f93a32)

Now, go to Dashboard → Manage Jenkins → Tools

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/5e2a237a-7b64-4f23-8b6d-442b8ccfb50c)

Add DockerHub Username and Password under Global Credentials

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/e4a32ddd-3717-4cf7-847c-7d986bb418c9)

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/fcf3062a-fef1-4518-9926-7cb55e8457a3)

Add this stage to Pipeline Script (Insert your own TMDB API keyt o build the docker image.)

```
stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=xxxxxxxxxxxx -t netflix ."
                       sh "docker tag netflix ravdas/netflix:latest "
                       sh "docker push ravdas/netflix:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image ravdas/netflix:latest > trivyimage.txt" 
            }
        }
```

You will see the output below.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/caf7d979-d012-48e8-a1c2-0123ff4e7a07)



When you log in to Dockerhub, you will see a new image is created

![dockregis (1)](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/77cafdd7-f276-4ba5-9af8-ff1a58f7af56)


Now Run the container to see if the game coming up or not by adding the below stage.

```
stage('Deploy to container'){
            steps{
                sh 'docker run -d --name netflix -p 8081:80 ravdas/netflix:latest'
            }
        }
```

Stage view

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/8b6546a8-0823-4621-a28e-414e62125c3e)

Visit ``` <Jenkins-public-ip:8081> ```

You will get below output,

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ed3dfbb9-b20c-499e-aa56-c2fa89d8ba16)


### Kuberenetes Setup

Install Kubectl on Jenkins machine also.

```
sudo vi kube.sh
```

```
sudo apt update
sudo apt install curl
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```
```
sudo chmod +x kube.sh
```
```
./kube.sh
```

Executer two Ubuntu 20.04 instances one for k8s master and the other one for worker. (t2.medium and 8GB storage)

![instant](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/ea1dd551-d871-43c4-8622-653497da538a)

Connect your both master and worker machines to Putty or Mobaxtreme using ssh like earlier using the keypair(.pem) file.

![connect ssh](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/4b6b91de-25f6-4f48-9385-26d538e2fd1d)

1 - Change hostname in CLI of master Node -  Run on master node machine

```
sudo hostnamectl set-hostname K8s-Master
```
```
exec bash
```

1.1 - Change hostname in CLI of worker Node -  Run on worker node machine

```
sudo hostnamectl set-hostname K8s-Worker
```
```
exec bash
```

2 - Install Docker and Kubernetes on both master & worker  -  Run on both master and worker node machines

```
sudo apt-get update 
sudo apt-get install -y docker.io
sudo usermod –aG docker Ubuntu
newgrp docker
sudo chmod 666 /var/run/docker.sock
```
```
sudo apt-get update
```
```
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

3 - Initialize Kubernets on master Node - Run on master node machine

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
# in case your in root exit from it and run below commands
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

![11](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/6059a42b-50dc-4b3f-9952-34a41026fa12)

4 - Join worker node with the master node -  Run on worker node machine

```
sudo kubeadm join <master-node-ip>:<master-node-port> --token <token> --discovery-token-ca-cert-hash <hash>
```

![12](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/894adb5d-5dbf-4ff0-9ce7-508590c3ca1a)

Check the 

![13](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/f42ab47b-e8c3-4b1a-a426-8940ec745a58)

Copy the config file to Jenkins master or the local file manager and save it

