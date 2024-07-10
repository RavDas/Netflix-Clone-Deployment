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



Go to your Gmail and click on your profile

Then click on Manage Your Google Account –> click on the security tab on the left side panel you will get this page(provide mail password).
