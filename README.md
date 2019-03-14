What is a host?

The host is where the tasks get run. It can be any number of remote hosts that you have SSH access to, or localhost.
Your hosts respective IP addresses or hostnames need to be stored in an inventory file for Ansible to be aware of them. Later when you run Ansible, you will specify one or more hosts, or groups of hosts, defined in the inventory file.

Creating an inventory

In this tutorial you have access to one host named host01. To let Ansible know about it, put its hostname in an inventory file. Let's call our inventory myhosts, with one group called group1.

echo "[group1]" > myhosts

Then add the host to the group:

echo "host01 ansible_ssh_user=ubuntu" >> myhosts

Here we're also passing the username to use for SSH access, as an inventory parameter.


Running Ansible Ad-hoc Tasks
To run an ad-hoc task, using a single module, use ansible.
For example, to check the current date and time of the remote host:

ansible group1 -i myhosts -m command -a date

(if the command fails the first time, try again in few seconds, as the test host may not yet have been provisioned)

group1 = group to run against. You can also use the name of an individual host, so host01 would work just as well for this example.
myhosts = name of the inventory file listing all available hosts
command = module to use (the command module returns the result of a bash command)
date = argument for the module, in this case a standard shell command date.
When you run the command above, Ansible establishes an SSH connection with the remote host, executes the command, captures the output and returns it.

Running a playbook Playbook
A playbook is an entirely different way of running Ansible, that is far more powerful.

What is a play?

A play is a set of tasks mapped to a set of hosts. Plays are organised inside a text file called a playbook.
Running a playbook
This playbook runs one task, on our one host, host01. Note the indentation - it's important for how the file gets parsed. Blank lines are ignored, but makes the playbook more readable for humans.

---
- hosts: host01

    become: true

    tasks:
      - name: ensure latest sysstat is installed
        apt:
          name: sysstat
          state: latest
          
The above code is already in a file called site.yml. Let's check the contents:
cat site.yml
To run the playbook, use the ansible-playbook command with the inventory file myhosts:

ansible-playbook -i myhosts site.yml

Ansible should return the result 'Changed=1', indicating that the package was installed.

Playbook breakdown

What happened here?
--- denotes the beginning of a YAML file
hosts: host tells Ansible to run the tasks on the host host
become: true makes all your tasks run as sudo
- name: is basically a comment, describing what the task does
apt: specifies the module we want to use
name: is an argument to the apt module, that specifies the name of the package to install.
To see all arguments for a specific module, allowed values, and other details, you can use the CLI documentation that is included with Ansible:

ansible-doc apt

To close the documentation, enter q in the terminal.

NOTE: This tutorial is using Ubuntu. If your own hosts are RHEL/CentOS, you should use the yum module instead.
