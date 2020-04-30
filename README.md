# CyberSecurity
Network Diagrams, scripts, etc

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Network Diagram](https://github.com/jt-cs/CyberSecurity/blob/master/Diagrams/Azure_VM_Diagram.PNG)

[These files](https://github.com/jt-cs/CyberSecurity/tree/master/Ansible) have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.



This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
-
Load balancing ensures that the application will be highly available, in addition to restricting connections to the network.
-
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log data and system metrics.
-
The configuration details of each machine may be found below.

| Name       | Function        | IP Address    | OS    |
|------------|-----------------|---------------|-------|
| Jump Box   | Gateway         | 10.0.0.1      | Linux |
| Elk Server | Data Logs       | 52.142.23.138 | Linux |
| DVWA-VM1   | Container Host  | 10.0.0.7      | Linux |
| DVWA-VM2   | Redundancy Host | 10.0.0.9      | Linux |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
67.250.163.35

Machines within the network can only be accessed by Jump Box.

Jump Box, 23.99.68.22 (10.0.0.6)

A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses |
|------------|---------------------|----------------------|
| Jump Box   | Yes                 | 67.250.163.35        |
| Elk Server | No                  | 10.0.0.6             |
| DVWA-VM1   | No                  | 10.0.0.6             |
| DVWA-VM2   | No                  | 10.0.0.6             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it...
- Saves time by streamlining setting up multiple servers by running a playbook file that runs configuration commands on a specific group of servers.

The playbook implements the following tasks:
- select server group to run commands on 
- (optional) run sysctl -w vm.max_map_count=262144
- install docker.io
- install python-pip and docker
- download and start sebp/elk container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps screenshot](https://github.com/jt-cs/CyberSecurity/blob/master/Ansible/docker_ps_output.PNG)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.7 and 10.0.0.9

We have installed the following Beats on these machines:
- Filebeat and MetricBeat

These Beats allow us to collect the following information from each machine:

- Filebeat collects log data, an example would be an http request from someone visiting your webpage. Metricbeat collects system metric data and metrics on services running on the system, an example would be the memory usage of the Apache service. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the ./filebeat.yml (config file) to /etc/filebeat/.
- Update the hosts file to include the Elk Servers group
- Run the playbook, and navigate to localhost/setup.php (localhost= server ip & port) to check that the installation worked as expected.

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
---
- name: Installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: ./filebeat.yml
      dest: /etc/filebeat/

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start
