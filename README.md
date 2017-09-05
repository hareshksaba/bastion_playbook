
1. Playbook Structure

bastion_playbook/
  ansible.cfg
  inventory
  ssh.cfg

2. inventory file

[bastion]
bastion_host_ip

[app_servers]
Target_host_ip

3. ssh.cfg file

Host bastion
  Hostname bastion_host_ip
  User bastion_host_username
  IdentityFile ~/.ssh/id_rsa
  PasswordAuthentication no
  ForwardAgent yes
  ServerAliveInterval 60
  TCPKeepAlive yes
  ControlMaster auto
  ControlPath ~/.ssh/ansible-%r@%h:%p
  ControlPersist 15m
  ProxyCommand none
  LogLevel QUIET

Host Target_host_ip.*
  User Target_host_username
  IdentityFile ~/.ssh/id_rsa
  ServerAliveInterval 60
  TCPKeepAlive yes
  ProxyCommand ssh -q -W %h:%p bastion_host_username@bastion_host_ip
  LogLevel QUIET
  StrictHostKeyChecking no

4. ansible.cfg file

[ssh_connection]
ssh_args = -F ssh.cfg
control_path = ~/.ssh/mux-%r@%h:%p


Test Command

- ssh -F ssh.cfg bastion_host_ip -v
- ssh -F ssh.cfg Target_host_ip -v
- ansible app_servers -i inventory -u Target_host_username -m ping
