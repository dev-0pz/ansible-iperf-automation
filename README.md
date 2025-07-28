# ansible-iperf-automation
A comprehensive infrastructure automation project that deploys a complete network performance testing environment on OpenStack using Ansible, featuring real-time monitoring with Grafana and persistent metrics storage in MySQL.
🎯 Project Overview
This project automates the deployment and configuration of a network performance testing infrastructure on OpenStack. It creates multiple VMs, configures iPerf3 for network testing, stores results in MySQL, and visualizes performance metrics through Grafana dashboards.

🛠️ Technology Stack

Automation: Ansible 2.9+ with custom roles and playbooks
Performance Testing: iPerf3 for network throughput testing
Database: MySQL for metrics storage
Monitoring: Grafana with custom JSON dashboards
Security: SSL/TLS encryption, Ansible Vault for secrets
Languages: Python, Ansible/YAML, Bash, SQL, JavaScript

quick startup guide 

git clone https://github.com/dev-0pz/ansible-iperf-automation.git

cd ansible-iperf-automation

source your-openstack-rc.sh

cp group_vars/all.yml.example group_vars/all.yml
# Edit all.yml with your environment details

ansible-playbook playbooks/main.yml --ask-vault-pass

(hardcode the floating ips in, if you want to use floating ips)

ansible-playbook playbooks/01-create-vms.yml

ansible-playbook playbooks/02-configure-iperf.yml

ansible-playbook playbooks/03-run-tests.yml

ansible-playbook playbooks/04-store-results.yml

sudo mysql

CREATE USER 'iperf_user'@'localhost' IDENTIFIED BY 'changeme';

GRANT ALL PRIVILEGES ON iperf_results.* TO 'iperf_user'@'localhost';

FLUSH PRIVILEGES;

EXIT;

ansible-playbook playbooks/05a-create-grafana-vm.yml

ssh -L 3000:<vm-ip>:3000 -i /home/User/.ssh/<key> ubuntu@<vm-ip>
MAKE SURE YOU ARE USING THE SAME SECURITY GROUP WITH 3306 and 22 so the vms can communicate

sudo snap install grafana
sudo snap services grafana


ansible-playbook playbooks/05b-create-grafana-dashboard.yml
  -e grafana_host=your-grafana-server \
  -e mysql_host=your-mysql-server \
  -e mysql_database=iperf3 \
  -e mysql_user=iperf3user \
  -e mysql_password=yourpassword


http://<grafana-vm-ip>:3000
change the password and ip when logged in on grafana


Name: iPerf3-MySQL

Host: <YOUR-MYSQL-HOST-IP>:3306
(This is the IP where your MySQL is running - likely your Ansible VM)

Database: iperf_results

User: grafana_reader

Password:  whatever password you set in your vault


ansible-playbook playbooks/06-cleanup.yml



experimental: wouldn't recommend
ansible-playbook playbooks/main.yml
