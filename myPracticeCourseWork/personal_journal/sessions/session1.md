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



## Summary of learning
*What did you learn through these exercises*
