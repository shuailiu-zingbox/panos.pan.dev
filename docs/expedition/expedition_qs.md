---
id: expedition_qs
title: Expedition 2.0 Installation
sidebar_label: Installation
hide_title: true
description: Expedition 2.0 Installation
keywords:
  - pan-os
  - panos
  - xml
  - api
  - expedition
  - migration
  - firewall
  - configuration
  - expedition
image: /img/expedition.png
---

:::note 
Expedition 1.x is the only supported version at time. Expedition 2.0 is scheduled to release in April, 2022. Learn more about the release at our [Live Community](https://live.paloaltonetworks.com/t5/expedition-articles/expedition-2-0-release-date-postponed/ta-p/423747) site.
:::

## Install Expedition

Expedition 2.0 is in constant development to cover new functionalities available in the market and to correct implementation issues that are identified in the code. To keep the Expedition instance up to date, the tool is initially provided as a Debian package. In the future we will also offer containerized flavors of the tool to allow installing it on other platforms rather than Debian based ones.  

:::note
The installation process does not support migrating Expedition 1 instances to Expedition 2.0  
:::

## System Requirements

As mentioned before, Expedition 2.0 is provided as a Debian package (that may have additional dependencies to be satisfied). Specifically, the package has been prepared to be installed in an Ubuntu 18.04 64-bit server.
The following link offers an official download image for the supported machine, Ubuntu 18.04.x LTS (AMD 64) Server
https://releases.ubuntu.com/18.04/  

The Ubuntu server will need to be provisioned by the end-user. Options include downloading and installing the Ubuntu 18.04 ISO (link above) onto a customer managed server or to provision an Ubuntu 18.04 LTS virtual server available from Google Cloud, AWS or Azure.  

When preparing your VM to host Expedition, consider the following recommended specifications depending on the type of project (migration) that you will cover with it.  

| Primary usage | Description | Recommended Compute Resources |
|-|-|-|
| Migration | Involving configuration conversions, but it will not involve traffic log analysis | 4 CPU/Cores<br/>8 GB RAM<br/>40 GB SSD storage<br/>1 nic with 100Mbps/1Gbps |
| Machine Learning (small environments) | Involving traffic log analysis covering periods of 30 days and 300MBs/day | 8 CPU/Cores<br/>16 GB RAM<br/>1 x 10GB (OS related data)<br/>1 x 150 GB SSD (Project Settings and parquet files)<br/>1 x 1 TB (Traffic and other log files) <br/>1 nic with 100Mbps/1Gbps |
| Machine Learning (large environments) | Involving traffic log analysis covering periods of more than 30 days and 300MBs/day | 8 CPU/Cores<br/>32 GB RAM<br/>1 x 10GB (OS related data)<br/>1 x 500 GB SSD (Project Settings and parquet files)<br/>1 x 4 TB (Traffic and other log files) <br/>1 nic with 100Mbps/1Gbps |

Table1: Expedition VM recommended system requirements 

## Installing the Expedition Packages

Expedition2 should be installed in a clean VM Ubuntu 18.04 AMD64 Server using an installation script that can be downloaded from

https://conversionupdates.paloaltonetworks.com/initSetup2beta.sh

The script will perform some initial verifications, including the existence of a user with username “expedition”, and that the Expedition package has not been installed in advance, so it would not modify the settings of an already installed Expedition VM.

This script will prepare the VM to host the different packages necessary for using Expedition 2, including the web service -based API, the converter, the JS Web UI, rsyslog for future syslog service support, Java and Spark for future traffic log analysis and additional required services such as rabbitmq, zip, and SSH.  

:::note
 This script requires communication to a certain number of apt-get repositories, including conversionupdates.paloaltonetworks.com . It is therefore necessary to allow APT-GET communications to be established from the VM that will host Expedition 2.0. In case of installation failure, make sure that these communications are successful.	
:::

The installation of all those modules would require internet connectivity, mainly to:  

Ubuntu source repositories (http://archive.ubuntu.com )  

Expedition packages (https://conversionupdates.paloaltonetworks.com )  

RabbitMQ for internal messaging (http://www.rabbitmq.com )  

Once a VM has been created with the required specifications (CPU, RAM and Network interfaces), retrieve the installer and execute it with the following commands  
```bash
cd ~
wget https://conversionupdates.paloaltonetworks.com/initSetup2beta.sh
chmod +x initSetup2beta.sh
sudo ./initSetup2beta.sh | tee output.log  
```  

Notice that we are downloading the installation script, making it executable (+x), launching it with sudo privileges and storing the installation output messages into a file called output.log.
The process is no human intervention required.
After the installation, the following changes will have been applied:  

- **Users**  
     - www-data will belong to the expedition group  
     - root password set to “paloalto”  


- **SSHD with enabled remote access for:**  
    - User: root 		Password: paloalto  
    - User: expedition	Password: paloalto  

- **Installation of MySQL server 5.7**  
    - User: root		Password: paloalto  

- **Apache + PHP 7.2**  
    - User: admin		Password: paloalto  

- **Rsyslog** 
    - Firewalld service initiated allowing connections to:
      - tcp/22 (ssh remote access)  
      - tcp/80   (initial unsafe web interface)  
      - tcp/443 (SSL web API)  
      - tcp/4050-4070 (future Spark connections)  
      - tcp/5050-5070 (future Spark connections)  

- **Filesystem**  

  - /home/userSpace will be created owned by www-data  
  - /data will be created owned by www-data  
  - /var/expedition-converter will be created with converter module  
  - /var/www/html/expedition-api will be created with API module  

## Update Expedition

Issue below commands to update your Expedition to a newer version release:  


```console
$sudo apt-get update
$sudo apt-get install expedition2-beta expedition2-converter-beta
```
:::note
If you just completed the installation in previous step, you do not need to issue above commands since the installation package you ran should contain the latest release. 
:::

## Accessing the Expedition GUI

After the installation is complete, reach your web browser to navigate to the front-end.
An initial Expedition web interface is provided through the following route (you may have to substitute the ExpeditionIP by the IP assigned to your VM.)  

```console
https://<YourExpeditionIP>    
```

For instance:
https://<span></span>192.168.64.138
