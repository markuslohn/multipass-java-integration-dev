# multipass-java-integration-dev
A java based development environment using multipass.io running on a MacOS M2 system. The way of operation is based on the approach of Vagrant+VirtualBox.

![](./assets/multipass-macos-setup.png)

It contains the following tools

- Java 17 
- Maven
- OpenShift Client
- NodeJS and npm

## Setup

### Prerequisites

In order to install and use the development environment, the following requirements must be met.

- Install and configure [Multipass](https://multipass.run/). It is possible to use [Brew](https://brew.sh/) to install Multipass on MacOS. Follow the steps described in the [documentation](https://multipass.run/docs/installing-on-macos).

- Install and configure [Ansible](https://www.ansible.com/)


### Create or update VM

1. Prepare VM configuration

The configuration settings are made in the file ```<project-root>/vm-config.yaml```. All settings are explained in this file.


2. In order to setup a new multipass VM navigate to ```<project-root>``` folder and run the following command:

```bash
ansible-playbook init.yaml
```


### Provisioning

Provisioning can be performed both by the host and directly within the VM. Provisioning is also performed via Ansible.

1. In order to provision a multipass VM navigate to ```<project-root>``` folder and run the following command:

```bash
ansible-playbook provision.yaml
```

Alternatively, provisioning can also be performed within the VM using Ansible. The following steps are required for this:

1. Modify Ansible roles in folder ```<project-root>/vm-provisioning```

2. Connect to the VM via SSH

```bash
ssh <vmuser>@IP-Address
``` 

**Notes:**  
- vmuser is configured in ```<project-root>/vm-config.yaml```.
- IP-Address can be obtain using ```multipass info <vmname>``` or ```multipass list```.

3. Navigate to ```/multipass/vm-provisioning```

4. Exceute the following command:

```bash
ansible-playbook playbook.yaml
```

## Insights

The creation of a VM is done in 4 steps. Basically the steps can be influenced by configuration settings in the file ```<project-root>/vm-config.yaml```. The creation is controlled by Ansible.

In step 1 a cloud-init.yaml is created. The file contains the user and public keys and defines basic software that should be installed automatically when the VM is created.

In step 2, the VM is created and configured with Multipass.io.

In step 3, different mounts are defined in Multipass.io to exchange data between the VM and the host.

As the 4th and last step, an Ansible script can be executed within the VM to install further tools and to adapt the VM to one's own needs. The "Provisioning" script can be executed again at any time. Steps 1 - 3 are actually only for creating the VM.


## Issues

### Maven repository can't be shared via a mount

When integrating Maven repository via a mount into the VM, no build is running correctly. The following error is shown:

```
[ERROR] Failed to execute goal io.quarkus.platform:quarkus-maven-plugin:2.16.7.Final:generate-code (default) on project messwerte-from-tredis-to-sap: Quarkus code generation phase has failed: java.io.IOException: Failed to create a new filesystem for /home/ubuntu/.m2/repository/io/quarkus/quarkus-apache-httpclient/2.16.7.Final/quarkus-apache-httpclient-2.16.7.Final.jar: /home/ubuntu/.m2/repository/io/quarkus/quarkus-apache-httpclient/2.16.7.Final/quarkus-apache-httpclient-2.16.7.Final.jar: Operation not permitted -> [Help 1]
```

I played with security options in the multipass mount command and also ensured that multipass has full access to my local harddrive. However until now it was not possible to avoid this error when running a Maven build. So I decided to unmount the Maven repository.




