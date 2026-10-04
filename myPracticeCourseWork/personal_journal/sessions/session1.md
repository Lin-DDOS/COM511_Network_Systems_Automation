[Personal Learning Record](../../personal_journal/personal_journal.md) | [Session Notes](../sessions/README.md) 

# Session 1

## Topics covered
*What topics were covered in this session*
- **Introduction to Vagrant:** concepts of using Vagrant for creating and managing portable, reproducible virtual machine envrionments similar to using Docker.
- **Virtualisation:** Configuring Vagrant with VirtualBox on window
- **Envrionment Configuration:** Setting up local paths using a sensible path without using OneDrive or network drives (e.g., C:\devel\vagrant)
- **Vagrant Workflow Commands:** Instial startup (vagrant up), accessing (vagrant ssh), managing states (vagrant halt, vagrant suspend), and cleanup (vagrant destory, vagrant box list).
- **Lab envrionment setup:** Working with pre-built boxes locally (e.g., bento boxes like Rocky Linux 9.6 and Ubunto 22.04).
 
## Personal Notes and research following this session
*Which class sessions and personal research refers to technology in this proposal. Link to examples.*
- **Vagrant & Hypervisors:** Explored Vagrant documentation alongside VirtualBox to understand how automated VM creation can simplifies developer onboarding compared to manual GUI setup.
- **Package management:** In Linux operating systems, it use package managers (like apt in Ubunto or dnf/yum in Rocky Linux) to download, install, update, and manage software packages from central repositories instead of manually searching for an installer on the web, all developer has to do is run commands like apt install apache2 to download and install software along with all its required dependencies.
- **provisioning:** In virtualisation tools like Vagrant, "provisioning" refers to the automated process fo setting up software, services, files, and settings within a newly created virtual machine (VM). It transforms a generic, fresh OS installation into a fully customised server tailored around the user's needs.
- **Shell scripts:** A shell script is a simple text file containing a sequence of terminal commands execued automatically in order.
> Example Vagrant commands used in this project:
```bash
# Start and provision the VM
vagrant up

# Gracefully shut down the VM
vagrant halt

# Restart the VM
vagrant reload

# Restart AND re-run provisioning
vagrant reload --provision

# Run provisioning without rebooting
vagrant provision

# SSH into the VM
vagrant ssh

# Check VM status
vagrant status

# Show all Vagrant environments
vagrant global-status

# Destroy the VM completely
vagrant destroy

# Force folder sync
vagrant rsync
```
The following commands were used to manage the Vagrant VM.[^vagrant]

[^vagrant]: Vagrant CLI reference – https://developer.hashicorp.com/vagrant/docs/cli





## Exercises and results
*What exercises did you complete. What results. Screen shots and notes*
# Exercise 1.3:
> Spin up a vagrant box and install Apache manually using the SSH terminal:
```bash
# To boot up vagrant
vagrant up

# To connect guest virtual machine via SSH
vagrant ssh

# Install the Apache2 web server package manually inside the guest OS
sudo apt-get update
sudo apt-get install -y apache2
sudo systemctl status apache2
exit
```
> To expose port 80 to see the Apache server on your host system:
```bash
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-22.04"

  # Forward guest port 80 (Apache) to host port 8080
  config.vm.network "forwarded_port", guest: 80, host: 8080
end

# Apply the network configuration without destroying the VM
vagrant reload
```
![Successful of loading Apache2 Default Page](myPracticeCourseWork/personal_journal/images/apache2.png)
*Caption: Vagrantfile configuration forwarding guest port 80 to host port 8080 followed by successful verifying it working.

> To automate the software installation during machine creation, add an inline shell provisioner block to the Vagrantfile:
```bash
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-22.04"

  # Network Configuration
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Provisioning Configuration
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y apache2
    systemctl enable apache2
    systemctl start apache2
  SHELL
end

# Apply the changes without destroying the VM
vagrant reload --provision
```

## Summary of learning
*What did you learn through these exercises*
