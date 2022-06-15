# Prometheus_and_Grafana_Installation
In this Repository, I clearly explained the steps to install and configure prometheus with grafana in Linux based Virtual Machine

    
Prometheus is an open-source monitoring system that is very lightweight and has a good alerting mechanism.


INSTALLATION AND CONFIGURATION PROMETHEUS

This guide explains how to install and configure the Prometheus on a Linux VM.

PREREQUISITE:

1.	Ensure that you have sudo access to the Linux server because the Prometheus server start and stop will only work in sudo mode.

2.	Importantly firewall rules opened for accessing Prometheus port 9090 on the server.




PROMETHEUS FILES SETUP STEPS:

STEP 1:  Update the yum repositories 
	sudo yum update -y

If you are using ubuntu use:
 sudo apt update -y

STEP 2: Go to the official Prometheus downloads page and get the latest download for Linux.

Download | Prometheus
![image](https://user-images.githubusercontent.com/64657574/173931003-8f0a617c-19b3-49cb-a176-c4775ede0228.png)

 
STEP 3: got to location bin
	cd /usr/local/bin

STEP 4: Download the file using “curl” on RedHat Linux or else use “wget” command for ubuntu Linux.
wget https://github.com/prometheus/prometheus/releases/download/v2.36.1/prometheus-2.36.1.linux-amd64.tar.gz

STEP 5: Once it downloads means, we need to un tar the file because the file is in a tar format.
	tar -xvf prometheus-2.36.1.linux-amd64.tar.gz

STEP 6: After the un tar process, we need to change our convention, because it is named Prometheus-2.36.1.linux-amd64 so it is hard to remember. For this purpose, only we will change the name from this to simply “Prometheus”.

mv prometheus-2.36.1.linux-amd64.tar.gz prometheus-files

STEP 7: Create a Prometheus user, required directories and make prometheus the user as the owner of those directories.

	sudo useradd –no-create-home –shell /bin/false prometheus
	sudo mkdir /etc/prometheus
	sudo mkdir /var/lib/prometheus
	sudo chown prometheus:prometheus /etc/prometheus
	sudo chown prometheus:prometheus /var/lib/prometheus

Note: Execute the commands one by one.

The file location is very much important if you are installed in any other location means, you need to change the location to /usr/local/bin and the ownerships.
	
  sudo cp prometheus-files/prometheus /usr/local/bin
  sudo cp prometheus-files/promtool  /usr/local/bin
  sudo chown prometheus:prometheus /usr/local/bin
  sudo chown prometheus:prometheus /usr/local/bin/promtool

STEP 8: Now we must move the console files from the current directory to the /etc/prometheus folder.
The reason behind this is we need to activate the service that’s why we moved.

	sudo cp -r prometheus-files /consoles /etc/prometheus
	sudo cp -r prometheus-files /consoles_libraries /etc/prometheus
	sudo chown -R prometheus:prometheus /etc/prometheus/consoles
	sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries

 
PROMETHEUS SETUP CONFIGURATION:

All the Prometheus configurations should be present in /etc/prometheus/prometheus.yml file.

STEP 1: Create the prometheus.yml file
	sudo vi /etc/prometheus/prometheus.yml

STEP 2: Copy the prometheus.yml from the repo and paste it into the file that you created.

STEP 3: Change the ownership of the file to prometheus user.
	sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml

Note: Indentations are much more important, so check again before saving.


PROMETHEUS SERVICE FILE CONFIGURATION 

STEP 1: Now we must create the prometheus service file.
	sudo vi /etc/system/system/prometheus.service
  
The service file is a must one because if the service file gets an error means we can’t start the prometheus service, so in this file indentation is necessary.

STEP 2: Copy the prometheus.service file from the repo and paste it in the prometheus.service you have been created.

STEP 3: Now we would restart the daemon by following the commands

	sudo systemctl daemon-reload
	sudo systemctl start prometheus

If these command gets executed means, prometheus service will get started working in port 9090.

STEP 4: Even though we started the prometheus, we need to check the status by the following  command
	
	sudo systemctl status prometheus

If it is started means, it will show the image like this
![image](https://user-images.githubusercontent.com/64657574/173931063-661d39a6-55eb-4b27-9436-6bc95e593c31.png)

 
ACCESS PROMETHEUS WEB UI

Congratulations, now everything is perfectly set, you can view prometheus in UI following command.

http://<ip-of-prometheus-instance>:9090
  ![image](https://user-images.githubusercontent.com/64657574/173931083-3e494236-6bbe-44e7-9154-2e78895e767f.png)


 
Prometheus Webpage


Up to this, you have created the prometheus tool in VM, you need to add the files that are to be monitored by using the node exporter, black box exporter, etc...
 
Node exporter is the best way to collect all the Linux server-related metrics and statistics for monitoring.

MONITORING LINUX SERVICES USING PROMETHEUS
	In this guide, you will learn how to set up Prometheus node exporter on a Linux server to export all node level metrics to the Prometheus server.

PREREQUISITE:
1.	Prometheus Node Exporter needs Prometheus server to be up and running. 
2.	Port 9100 opened in server firewall as Prometheus reads metrics on this port.

NODE EXPORTER SETUP FILES
	
STEP 1: Download the latest node exporter package, you should check the Prometheus link we downloaded before.

cd /tmp

wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz

STEP 2: Unpack the tar

	tar -xvf node_exporter-0.18.1.linux-amd64.tar.gz

STEP 3: Move the node export binary to /usr/local/bin
	sudo mv node_exporter-0.18.1.linux-amd64/node_exporter /usr/local/bin/

CREATE A CUSTOM NODE EXPORTER SERVICE

STEP 1: Create a node_exporter user to run the node exporter service.
	sudo useradd -rs /bin/false node_exporter

STEP 2: Create a node_exporter service file under systemd.
	sudo vi /etc/systemd/system/node_exporter.service

STEP 3:   Copy the node_exporter.service file from repo and paste it into the file you created.
	
STEP 4: Reload the system daemon and star the node exporter service.
sudo systemctl daemon-reload
	sudo systemctl start node_exporter


STEP 5: check the node exporter status to make sure it is running in the active state.
	sudo systemctl status node_exporter

STEP 6:  Enable the node exporter service to the system start up.
	sudo systemctl enable node_exporter

Now, node exporter would be exporting metrics on port 9100. 
You can see all the server metrics by visiting your server URL on /metrics as http://<server-IP>:9100/metrics

Configure the Server as Target on Prometheus Server
Now that we have the node exporter up and running on the server, we must add this server as a target on the Prometheus server configuration.

STEP 1: Login to the Prometheus server and open the prometheus.yml file.

STEP 2: Under the scrape config section add the node exporter target as shown below. Change 10.142.0.3 with your server IP where you have setup node exporter. Job name can be your server hostname or IP for identification purposes.

Note: filename is update prom

STEP 3:  Restart the prometheus service for the configuration changes to take place.
	sudo systemctl restart Prometheus

Now successfully we have installed the Prometheus with node_exporter, then finally we will go for the Grafana.
  

In this guide, we will walk you through the following.
1.	Install and configure Grafana
2.	Add Prometheus data source to Grafana
3.	Creating dashboards from Prometheus metrics.
4.	Importing shared Grafana dashboards

Install and Configure Grafana

STEP 1: Get the latest rpm download link from the official download page and download it. 

curl -LO https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_5.1.4_amd64.deb

STEP 2: Install the packages

	sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_5.1.4_amd64.deb	
CONFIGURE GRAFANA		
STEP 1: Start and enable the grafana server.
	sudo systemctl start grafana-server
	sudo systemctl enable grafana-server
STEP 2: Access grafana UI on port 3000.
	http://<grafana_IP>:3000

ADDING PROMETHEUS SOURCE TO GRAFANA
  
Note: Make sure Prometheus endpoint is accessible from Grafana server.
  
Once it gets opened the username is admin and the password is admin.
  
After it will prompt you to enter a new password.
  
Then add the source name, Prometheus endpoint details and save it.

CREATE DASHBOARD FROM PROMETHEUS SOURCE
  
Select the create dashboard option as shown below.
  ![image](https://user-images.githubusercontent.com/64657574/173930876-7044dcaa-232f-4322-a41f-79cbdb4cc3b0.png)

 
Select the graph type. You can select the type based on the type of visualization and dashboard you need.
 ![image](https://user-images.githubusercontent.com/64657574/173930891-10a954ce-c421-49c6-bc14-b14f91ac9117.png)

  
Select the edit option from the panel top.
  ![image](https://user-images.githubusercontent.com/64657574/173930919-99fdd174-5d8e-438d-9d9d-3b498729ad46.png)

 
Select the Prometheus data source and enter the Prometheus expression that has to be graphed under the metrics tab. You can preview the graph using the preview button. Under the General tab, you can assign a name for the dashboard. Save the dashboard after preview. In the following example, we have given the expression for calculating CPU seconds every minute.
 ![image](https://user-images.githubusercontent.com/64657574/173930937-9052167a-0d7c-4284-ab36-21b31d451240.png)

Tada, Our Grafana Visualizer is now ready to use, You can also import your favorite dashboards from different sites.
	




