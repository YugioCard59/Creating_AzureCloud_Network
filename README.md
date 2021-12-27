# Creating_AzureCloud_Network
Basic steps to create a Azure based cloud network.

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Azure Cloud based Virtual Network topology:  

https://github.com/YugioCard59/Creating_AzureCloud_Network/blob/main/Cloud_Network.png

### Description of the Topology

The above virtual network topology shows that within the entire Red-Team Resource group consists of two virtual networks (vNet) that are allowed to communicate using internal IPs.  The RedTeam vNet consists of the Jump_box_provisioner VM that can communicate with the external IP user and internal load balancer.  The load balancer is located within the RedTeam vNet and servers as the gateway node directing internal traffic from the Jump_box_provisoner and webservers 1 and 2 containers and directing external traffic from the internet to the containers and provisioner.  This communication is limited by the firewall rules set within Azure.  vNet ELK-SERVER can communicate with the load balancer and provisioner VM within Red-Team vNet.  ELK-SERVER acts like a microservice housing the database for the resource group.  Each vNet has its own security group that sets the firewall rules.  External communication is further limited by use of ssh key pairs to communicate between the load balancer and containers and between the vNets and the external user.


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the file may be used to install only certain pieces of it, such as Filebeat.

  ELK configuration file:  

https://github.com/YugioCard59/Creating_AzureCloud_Network/blob/main/yamildocker.txt


The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.

Load balancers help protect the availability of the container webservers deployed within the virtual network by spreading out areas of high traffic to additional available webservers or if a webserver becomes compromised the load balancer can divert that traffic to another non-compromised webserver.  In this way the load balancers help ensure availability to web services.  Load balancers that are configured via Azure virtual firewall rules will also protect the confidentiality and integrity of data because access to the virtual network is limited by what traffic the load balancer is configured to allow.

In this virtual network a provisioner VM named Jump_box_provisioner is deployed to allow fast and secure distribution and sharing of resources such as OS and kernel applications to containers within the virtual network.  The provisioner VM also acts like a gateway node between external IP addresses and internal IP addresses.  This VM is configured via Azure firewall rules, RDP rules and the use of SSH key pairs used between the external user and internal containers to communicate with the Jump_box_provisioner.  This protects data confidentiality, availability and integrity because the rules applied to the VM limit the inbound and outbound traffic through the Jump_box_provisioner.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network traffic and system files.

Filebeat is configured to watch for changes to system files.
Metricbeat records machine uptime and CPU usage.


The configuration details of each machine may be found below.


| Name                 | Function        | IP Address                                 | Operating System |
|-----------------------|:-----------------:|----------------:|-------------------------:|:-------------------------|
| Jump Box           | Gateway       | 10.0.0.4 & dynamic public IP    | Linux                       |
| webserver1         | webserver    | 10.0.0.5 & 20.124.0.105           | Linux                       |
| webserver2         | webserver    | 10.0.0.6  & 20.124.0.105          | Linux                       |
| ELK-SERVER     | database      | 10.1.0.4 & dynamic public IP    | Linux                      |
| Load Balancer     | Gateway      | 20.124.0.105                             | Windows                |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the load balancer machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:   10.0.0.5, 10.0.0.6, 20.124.0.105, 10.0.0.4, and 10.1.0.4

Machines within the network can only be accessed by either load balancer or ssh keys.
The RedTeam Azure Load Balancer and external user public IP has access to the ELK-SERVER VM.  The IP of the load balancer is 20.124.0.105.

A summary of the access policies in place can be found in the table below.

| Name                | Publicly Accessible                                            | Allowed IP Addresses     |
|----------------------|-----------------------------------------------------------------:|------------------------------------|
| Jump Box        | limited w/ ssh keys                                              | 10.0.0.5 10.0.0.6               |
|		    |							  | 20.124.0.105 10.1.04        |
|                           |                                                                               | External user IP	          |
| webserver1      | limited by load balancer, to port 80 & ssh keys    | 10.0.0.4 Ext. user IP   |   
|                           |                                                                                | 20.124.0.105                    |
| webserver2      | limited by load balancer, to port 80 & ssh keys    | 10.0.0.4 Ext. user IP   |
|                           |                                                                                 | 20.124.0.105                   |  
| ELK-SEVER     | limited w/ ssh keys                                              | 20.124.0.105 10.0.0.4      |
|                           |                                                                               | 10.0.0.5 10.0.0.6               |
|                           |                                                                               | External user IP              |
| Load balancer  | limited to port 80                                                 | 10.0.0.5 10.0.0.6              |
|                           |                                                                               | 10.0.0.4 10.1.0.4               |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous.
Automation of machine configuration is advantageous because less human error is introduced, machines can be duplicated, started and killed quickly through automation.  Automation can also be updated by creating different versions of the configuration files.  An advantage of configuration with Ansible is the ease of reading and writing a YAML file.

The playbook implements the following tasks:
Install docker.io
Install python3-pip
Install Docker python module
Use sysctl module
Download and launch a docker elk container
Enable service docker on boot


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
https://github.com/YugioCard59/Creating_AzureCloud_Network/blob/main/docker-ps.png


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
10.0.0.6, 10.0.0.5 and 20.124.0.105

We have installed the following Beats on the above specified webserver machines:
System Module:  tracks system log files from across different services
Apache Module:  tracks Apache log files that give information about access and errors created by Apache HTTP server
Auditd Module:  enable Auditd event log files from Linux Audit Framework
Elasticsearch Module:  ingests log files to be parsed and analyzed 
Haproxy Module
Icinga Module:  analyzes main, debug, and startup logs of Icinga
IIS Module:  analyzes access and error logs created by the Internet Information Services (IIS) HTTP server
Kafka Module: collects Kafka Apache streaming logs
Kibana Module
Logstash Module:  tracks logstash logs
Mongodb Module
MySQL Module:  collects MySQL logs
Nats Module
Nginx Module:  collects Nginx HTTP server logs
Osquery Module:  tracks and decodes the result logs written by osqueryd in the JSON format
PostgreSQL Module:  collects PostgreSQL relational database logs
Redis Module
Google Santa Module:  collects logs from Google Santa
Traefik Module

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-playbook.yml (https://github.com/YugioCard59/Creating_AzureCloud_Network/blob/main/install_launch_filebeat_yml.txt) and my-playbook.yml (https://github.com/YugioCard59/Creating_AzureCloud_Network/blob/main/Filebeat-configuration-yml.txt) file to Ansible control node.
- Update the playbooks files to include
For filebeat configuration playbook update with the following information:  Line #1106, the output.elasticsearch needs the IP address of the specific Elk server running in the virtual network.  In the same line, a password and username is required to be added and will be used as credentials.  Note line #1806, setup.kibana requires the IP address of the specific Elk server running in the virtual network.
For filebeat playbook update with following information:  "hosts" name is specific to containers/VMs that require Elk and filebeat monitoring.
- Run the playbook, and navigate to Kibana to check that the installation worked as expected.  This can be done in the command line with command:  curl http://10.1.0.4:5601 .  Note the IP address used is the ELK-SERVER private IP address.

The playbooks consist of the two playbooks mentioned above:  filebeat-playbook.yml and my-playbook.yml (links given above).  These playbooks are copied to the ansible directory within the ansible control node.

To update Ansible to run playbooks on specific machines then the ansible control node’s /etc/ansible/hosts file needs to be updated with the specific machine private IP address.

To specify which machine(s) to install the ELK server run the updated my-playbook.yml (link given above) file.  To specify which machines(s) to install Filebeat run the updated filebeat-playbook.yml (link given above) file.

Check that ELK-SERVER is running in the Kibana website by using the curl command provided above with the IP address specific to the ELK-SERVER private IP address.

Commands:
Run ansible playbook:  $ ansible-playbook <name-playbook.yml>
Update files:  $ nano <filename.yml>
Downloading playbook from internet:  $ curl <url path>
