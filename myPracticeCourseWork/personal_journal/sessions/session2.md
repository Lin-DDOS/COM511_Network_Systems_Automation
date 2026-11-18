# Session 2 — Automated Provisioning of Servers Using Ansible

## Topics covered

- **Vagrant provisioners:** Shell scripts that install and configure software after a virtual machine (VM) boots, building on the Session 1 Apache exercises.
- **Ubuntu vs Rocky Linux:** Ubuntu uses `apt` and the `apache2` service; Rocky Linux uses `yum` and `httpd`, with additional service and firewall configuration.
- **Deploying a web page:** Both Example 2-1 provisioners copy `examplewebpage.html` from `/vagrant` into `/var/www/html/`.
- **Three-machine lab:** Example 2-2 creates `ansible_controller`, `ubuntu_1` and `rocky_1` from one `Vagrantfile`.
- **Networking and users:** NAT provides internet access; a host-only network provides static addresses for VM communication. The lab uses `vagrant`, `ansible` and `admin` accounts.
- **Ansible:** An agentless automation tool maintained by the Red Hat ecosystem. The controller connects to hosts using SSH and executes modules and playbooks.

## Personal notes and research following this session

- **NAT vs host-only:** NAT provides internet access, while the host-only network (`192.168.56.0/24`) connects the host computer and lab VMs.
- **Static addresses:** The host uses `192.168.56.1`. The controller uses `192.168.56.10`, Ubuntu uses `192.168.56.20`, and Rocky Linux uses `192.168.56.30` on `eth1`.
- **SSH keys:** After switching to the `ansible` user with `sudo su ansible`, the shared key allows SSH without entering a password. Connecting from the `vagrant` account prompts for the lab password.
- **Agentless management:** No Ansible agent needs to run on managed hosts. The ping output reported Python 3.12 on Ubuntu and Python 3.9 on Rocky Linux.
- **Inventory and playbooks:** Inventory files define target hosts. `ansible -m ping` performs an ad-hoc connectivity test; `ansible-playbook` runs YAML task definitions.
- **Facts and roles:** Ansible facts identify the operating-system family (`Debian` or `RedHat`). A role can select `Debian.yml` or `Rhel.yml` accordingly.
- **SSH host keys:** Initial connections may fail until the host key is accepted. The lab commands used `StrictHostKeyChecking=no` to bypass the prompt; this is convenient for a controlled lab but should not be the default on production systems.
- **Configuration warning:** Ansible ignored `ansible.cfg` when running in the world-writable `/vagrant` directory.

**Further reading:** *Getting Started with Ansible*; *Ansible Fundamentals Beyond the First Playbook*.

## Exercises and results

### Exercise 2.1 — Apache using Vagrant provisioners

Example 2-1 contains separate Ubuntu and Rocky Linux projects. Both install a web server and copy `examplewebpage.html` from `/vagrant` to `/var/www/html/`.

**Ubuntu (`bento/ubuntu-24.04`):** Guest port 80 is forwarded to host port 8081. Apache is installed using `apt`.

```bash
apt-get update
apt-get install -y apache2
cp /vagrant/examplewebpage.html /var/www/html/examplewebpage.html
```

**Rocky Linux (`bento/rockylinux-9.6`):** Guest port 80 is forwarded to host port 8082. The provisioner installs `httpd`, starts the required services, and permits HTTP through the firewall.

```bash
yum install -y httpd
systemctl enable httpd
systemctl start httpd
systemctl enable firewalld
systemctl start firewalld
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
cp /vagrant/examplewebpage.html /var/www/html/examplewebpage.html
```

**Expected pages:**

- Ubuntu: http://localhost:8081/examplewebpage.html
- Rocky Linux: http://localhost:8082/examplewebpage.html

*Evidence:* No screenshot of this exercise was included in the Week 2 folder.

### Exercise 2.2 — Three machines, host-only networking and shared SSH keys

Example 2-2 launches three VMs, each with a NAT interface (DHCP, gateway `10.0.2.1`) and a host-only interface.

| VM | Operating system | Host-only IP | Purpose |
| --- | --- | --- | --- |
| `ansible_controller` | Ubuntu 24.04 | `192.168.56.10` | Ansible controller |
| `ubuntu_1` | Ubuntu | `192.168.56.20` | Managed host |
| `rocky_1` | Rocky Linux 9.6 | `192.168.56.30` | Managed host |

The lab defines `vagrant` (SSH key only), `ansible` (shared SSH key and lab password), and `admin` (password authentication only). The shared key is stored in `/vagrant/.ssh-keys`.

Start the machines and connect to the controller:

```bash
cd sessions/session2/vagrant-examples/example2-2
vagrant up
vagrant status
vagrant ssh ansible_controller
```

Test SSH from the `vagrant` account (prompts for the lab password):

```bash
ssh ansible@192.168.56.20
ssh ansible@192.168.56.30
```

Switch to the `ansible` account and connect using the shared key:

```bash
sudo su ansible
ssh 192.168.56.20
ssh 192.168.56.30
```

*Evidence:* No screenshot of this exercise was included in the Week 2 folder.

### Exercise 2.3 — Ansible ping

From the `ansible` account, the `project-ansible2-1` inventory targets `192.168.56.10`, `192.168.56.20` and `192.168.56.30`.

```bash
vagrant ssh ansible_controller
sudo su ansible
cd /vagrant/ansible/project-ansible2-1
ansible -i inventory.ini all -m ping \
  -e "ansible_ssh_common_args='-o StrictHostKeyChecking=no'"
```

**Result:** All three hosts returned `SUCCESS` and `ping: pong`. Python 3.12 was detected on `.10` and `.20`, and Python 3.9 on `.30`. Ansible also warned that `/vagrant` was world-writable.

![Ansible ping showing successful responses from all three hosts](session2-assets/ansible-ping.png)

*Screenshot: All three Ansible ping tests returned `pong`.*

The project also contains `inventory2.ini` for password-based login as `admin`, but no screenshot of that test was included.

### Exercise 2.4 — Ansible playbooks

A playbook is a YAML file containing tasks. `playbook-facts-anddebug.yml` collects system facts and displays each host's operating-system family.

```bash
cd /vagrant/ansible/project-ansible2-2
ansible-playbook playbook-facts-anddebug.yml \
  -i inventory.ini \
  -e "ansible_ssh_common_args='-o StrictHostKeyChecking=no'"
```

**Result:** The debug output reported `Debian` for `ansible_controller` and `ubuntu_1`, and `RedHat` for `rocky_1`. The play recap reported `ok=2`, `changed=0`, and `failed=0` on each host.

![Ansible facts playbook output and play recap](session2-assets/ansible-facts.png)

*Screenshot: The facts playbook identified the operating-system families and completed without failures.*

The project also includes these Apache installation playbooks:

- `playbook-ubuntu-apache.yml`: Uses `apt` to install `apache2` on `ubuntu_hosts`.
- `playbook-rocky-apache.yml`: Uses `yum` to install `httpd`, configures `firewalld`, and copies the example page.
- `playbook-all-apache.yml`: Uses the `installApache` role to select `Debian.yml` or `Rhel.yml` according to `os_family`, then copies the example page to all hosts.

Commands:

```bash
ansible-playbook playbook-ubuntu-apache.yml -i inventory.ini
ansible-playbook playbook-rocky-apache.yml -i inventory.ini
ansible-playbook playbook-all-apache.yml -i inventory.ini
```

**Expected pages:**

- http://192.168.56.20/
- http://192.168.56.30/
- http://192.168.56.10/examplewebpage.html

*Evidence:* No screenshots of these three Apache playbook runs were included in the Week 2 folder.

## Summary of learning

- Ubuntu (`apache2` / `apt`) and Rocky Linux (`httpd` / `yum` and `firewalld`) require different installation and configuration steps to achieve the same outcome.
- A host-only network with static IP addresses and shared SSH keys allows the `ansible` account to manage all three VMs from the controller.
- The Ansible ping module confirmed connectivity to all three hosts, while the facts playbook identified Ubuntu hosts as `Debian` and Rocky Linux as `RedHat`.
- An Ansible role can use `os_family` to select operating-system-specific tasks from a single playbook.

