## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/95233170/161276294-949d4a64-d3cf-4987-8763-24ce4f65cea8.png)



These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the __playbook yml and config__ file may be used to install only certain pieces of it, such as Filebeat.

  - TODO: Enter the playbook file.

This document contains the following details:

- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly __redundant, in addition to restricting traffic__ to the network.
- What aspect of security do load balancers protect? What is the advantage of a jump box?

__Load balancers play a role to maintain "availability" of the information. In case of a technical failure or system attacks from outside sources, load balancers reroute the traffic in between servers.__  

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the __network and system logs.__
- What does Filebeat watch for?

__Filebeat is a tool that monitors log files and locations and collects the data then forwards to place wherever it's specified. Having tool like filebeat makes certain tasks easier. For example, in an extensive corporate environment, we can gather all the log information instead of going them in one by one.__
- What does Metricbeat record?
 
__Metricbeat is a tool that gives us information such as the system performance and usage of CPU, Memory, RAM in a GUI format.__

The configuration details of each machine may be found below.
Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table.

| Name     | Function         | IP Address | Operating System |
|----------|------------------|------------|------------------|
| Jump Box | Gateway          | 10.0.0.4   | Linux            |
| Web-1    | Virtual Machine  | 10.0.0.9   | Linux            |
| Web-2    | Virtual Machine  | 10.0.0.10  | Linux            |
| Web-3    | Virtual Machine  | 10.0.0.8   | Linux            |
| ELK      | Virtual Machine  | 10.1.0.5   | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the __Jumpbox__ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Add whitelisted IP addresses

__my public IP__

Machines within the network can only be accessed by __from my workstation through Jump-Box via SSH to the virtual network__.
-  Which machine did you allow to access your ELK VM? What was its IP address?
  
__Jump-Box provisioner via SSH over port #22. Jump-Box has my public IP while ELK-VM has 10.1.0.5__ 

A summary of the access policies in place can be found in the table below.

| Name      | Publicly Accessible | Allowed IP Addresses |
|-----------|---------------------|----------------------|
| Jump Box  | Yes                 | Workstation publicIP |
| Web-1     | No                  | 10.0.0.4/JumpBox     |
| Web-2     | No                  | 10.0.0.4/JumpBox     |
| Web-3     | No                  | 10.0.0.4/JumpBox     |
| ELK-Server| No                  | Workstation publicIP |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- What is the main advantage of automating configuration with Ansible?
 
__Ansible allows users to manage, configure, update or run tasks on multiple servers from a single point rather than go into each servers and re-do every single tasks. This way a possible human error can be eliminated as well as a big time save can be accomplished.__

The playbook implements the following tasks:
- In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.
---
  
    name: Config Web VM with Docker
    hosts: elk
    become: true
    tasks:
   
    name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

    name: download and launch a docker elk container
    docker_container:
      name: elk
      image: sebp/elk:761
      state: started
      restart_policy: always

      published_ports:
       -5601:5601
       -9200:9200
       -5044:5044
       
    name: Enable service docker on boot
    systemd:
      name: docker
      enabled: yes
      
 - __In the example above, there is a section of a used yaml file in order to deploy ELK container in the Elk Server__
 - __First, it starts with name of the yaml.__
 - __Then hosts identifies where the yaml file will be deployed into.__
 - __become: true value shows a sort of execution of the tasks.__
 - __then under 'tasks' section all the commands can be scripted as blocks.__
 - __First block is to upload docker.io by using apt command. 'State: present' means is to install, unlike 'absent' to uninstall.__
 - __Second block is specifying download and launching Elk container, restart policy always means the container will be up and running whenever the Elk server is running.__
 - __Following blocks are to specify over which ports to establish access.__
 - __Last blcok is to enable docker service as the server powered on.__

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/95233170/161361048-bf935559-332d-457a-a796-3b3bef1d24ec.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- List the IP addresses of the machines you are monitoring

- __ELK Server: 10.1.0.5__
- __Web-1 : 10.0.0.9__
- __Web-2 : 10.0.0.10__
- __Web-3 : 10.0.0.8__

We have installed the following Beats on these machines:
- Specify which Beats you successfully installed
 
__Filebeat__

![image](https://user-images.githubusercontent.com/95233170/161408907-2050f4a3-ebaf-4f0d-b297-8f2f1b963d71.png)

__Metricbeat__

![image](https://user-images.githubusercontent.com/95233170/161363679-1715b077-872d-4f50-bf30-978f968671d8.png)

These Beats allow us to collect the following information from each machine:
- TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the __yml__ file into __/etc/ansible/ path.__
- Update the __hosts__ file to include __host name, tasks, state...___   
- Run the playbook, and navigate to __Kibana via ELK private IP__ to check that the installation worked as expected.

TODO: Answer the following questions to fill in the blanks:
- Which file is the playbook? Where do you copy it?
- Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?

__

- Which URL do you navigate to in order to check that the ELK server is running?

 __http://"elk-serverIP":5601/app/kibana__

As a *Bonus*, provide the specific commands the user will need to run to download the playbook, update the files, etc.
