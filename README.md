# Project-1
Project 1/ELK Deployment
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
Inline-style:
![alt text](https://github.com/boycolde/Project-1/blob/main/Diagrams/cloud%20homework.drawio.png "cloud diagram")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the readme file may be used to install only certain pieces of it, such as Filebeat.
ELK Install
Metricbeat playbook
Filebeat Playbook

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will disburse traffic efficiently , in addition to restricting access to the network.
Load balancers off-loading function defends organizations against distributed denial-of-service (DDoS) attacks. It does this by redirecting traffic from the corporate server for example to a public cloud provider. A jumpbox brings the advantage of protecting azure VM’s from being exposed to the public as its entry point is set via remote desktop protocol (RDP).

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to applications and system logs.
What does Filebeat watch for? Filebeat monitors log files or locations and collects log events and forwards them either to elasticsearch or logstash for indexing.
What does Metricbeat record? Periodically collects metrics from the operating system and from services running on the server.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| WEB 1    | Server   | 10.0.0.6   | Linux            |
| WEB 2    | Server   | 10.0.0.7   | Linux            |
| ELKVM    |Log Server| 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-personal ip address
Machines within the network can only be accessed by SSH.
Which machine did you allow to access your ELK VM? What was its IP address? Jumpbox (20.58.185.201) 

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | no (ssh)            | Personal             |  
|load balancer| yes              | Open                 |
| WEB 1    | No (ssh)            | 20.58.185.201        |
| WEB 2    | No (ssh)            | 20.58.185.201        |
| ELKVM    | no (ssh)            | 20.58.185.201        |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because its simple human readable automation that doesn't require special coding skills and tasks get executed in order. It also has strong app deployment, configuration management and workflow orchestration.

The playbook implements the following tasks:
Installs docker.io, pip3 and the docker module. 
 # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

  # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

  # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present
Increases the virtual memory
# Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
Use sysctl module
# Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
Download and launch docker (elk server)
# Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

   

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
 
![alt text](https://github.com/boycolde/Project-1/blob/main/Images/docker_ps_output.png "docker output")

This ELK server is configured to monitor the following machines:
Web 1(10.0.0.6)
Web 2(10.0.0.7)

Beats installed on following machines:
Filebeat
Metric Beat

These Beats allow us to collect the following information from each machine:
Filebeat is log data shipper for files thats installed as an agent on your servers like Web 1,2 as listed above. Filebeat monitors the log directories or specific log files and tails the files and forwards them either to Elasticsearch or Logstash for indexing.
Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash. An example would be the system module which lets you monitor your servers like web 1 and 2 for cpu,load,memory etc

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the configuration file to Web VMs.
- Update the /etc/ansible/hosts file to include the ip address of the Elk server VM and webservers
- Run the playbook, and navigate to http://[ELK_VM_Public_IP]:5601/app/kibana to check that the installation worked as expected.

Which file is the playbook? The filebeat-configuration
Where do you copy it? From /etc/ansible/files/filebeat-config/yml to /etc/filebeat/filebeat.yml

Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on? Update filebeat-config.yml and specify which machine to install by updating the host file with ip addresses of the web/elk servers and select which group to run on in ansible.

Which URL do you navigate to in order to check that the ELK server is running? http://[ELK_VM_Public_IP]:5601/app/kibana
