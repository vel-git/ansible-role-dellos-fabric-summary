ansible-role-dellos-fabric-summary
=====================================
This role is used to get show system information of all the switches in the fabric Dell EMC Networking Operating Systems.

Installation
------------

    ansible-galaxy install Dell-Networking.dellos_fabric_summary

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories or inventory, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``ansible_host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``ansible_port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_PORT option is used; it defaults to 22 |
| ``ansible_ssh_user`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_USER environment variable value is used  |
| ``ansible_ssh_pass`` | no       |            | Specifies the password that authenticates the connection to the remote device.  |
| ``ansible_become`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_BECOME environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``ansible_become_method`` | no       | enable, sudo\*   | Instructs the module to allow the become method to be specified for handling privilege escalation; if value is unspecified, the ANSIBLE_BECOME_METHOD environment variable value is used. |
| ``ansible_become_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``ansible_become`` is set to no this key is not applicable. |
| ``ansible_network_os`` | yes      | dellos10, null\*  | This value is used to load the correct terminal and cliconf plugins to communicate with the remote device. |

> **NOTE**: Asterisk (*) denotes the default value if none is specified.

Dependencies
------------

*xmltodict*  library should be installed to get show command output in dict format from xml.
To install the package use the *pip install xmltodict* command.

Example playbook
----------------

This example uses the *ansible-role-dellos-fabric-summary* role to completely get the show attributes. It creates a *hosts* file with the switch details and corresponding variables.

The *ansible-role-dellos-fabric-summary* role has a simple playbook that only references the *ansible-role-dellos-fabric-summary* role.

**Sample hosts file**

        site1-spine1 ansible_host=10.11.180.21 ansible_ssh_user=admin ansible_ssh_pass=admin ansible_network_os=dellos10
        site1-spine2 ansible_host=10.11.180.22 ansible_ssh_user=admin ansible_ssh_pass=admin ansible_network_os=dellos10
        site2-spine1 ansible_host=10.11.180.23 ansible_ssh_user=admin ansible_ssh_pass=admin ansible_network_os=dellos10
        site2-spine2 ansible_host=10.11.180.24 ansible_ssh_user=admin ansible_ssh_pass=admin ansible_network_os=dellos10
        [spine]
        site1-spine1
        site1-spine2
        site2-spine1
        site2-spine2
        [LeafAndSpineSwitch:children]
        spine

**Sample host_vars/site1-spine1**

    
    cli:
       host: "{{ ansible_host }}"
       username: "{{ dellos10_cli_user | default('admin') }}"
       password: "{{ dellos10_cli_pass | default('admin') }}"
       timeout: 300
    ansible_ssh_user: admin
    ansible_ssh_pass: admin
    ansible_network_os: dellos10

**Simple playbook to setup fabric summary - provision.yaml**

    ---
    - name:  show system summary command
      hosts: localhost
      gather_facts: False
      connection: local
      roles:
        - ansible-role-dellos-fabric-summary

**Run**

    ansible-playbook -i hosts provision.yaml

**Samaple Output**

    "results": [
        {
            "device type": "S6010-ON",
            "host": "10.11.180.21",
            "hostname": "host3",
            "inv_name": "site1-spine1",
            "node-mac": "e4:f0:04:9b:e5:dc",
            "service-tag": "D33FXC2",
            "software-version": "10.4.9999EX"
        },
        {
            "device type": "S6010-ON",
            "host": "10.11.180.22",
            "hostname": "host22",
            "inv_name": "site1-spine2",
            "node-mac": "e4:f0:04:9b:eb:dc",
            "service-tag": "J33FXC2",
            "software-version": "10.4.9999EX"
        },
        {
            "device type": "S6010-ON",
            "host": "10.11.180.24",
            "hostname": "site2-spine1",
            "inv_name": "site2-spine1",
            "node-mac": "e4:f0:04:9b:ee:dc",
            "service-tag": "343FXC2",
            "software-version": "10.4.9999EX"
        },
        {
            "device type": "S6010-ON",
            "host": "10.11.180.23",
            "hostname": "site2-spine2",
            "inv_name": "site2-spine2",
            "node-mac": "e4:f0:04:9b:f1:dc",
            "service-tag": "543FXC2",
            "software-version": "10.4.9999EX"
        }
    ]

(c) Copyright 2019 Dell Inc. or its subsidiaries. All Rights Reserved.

