Experiment 9 — Ansible: Configuration Management and Automation

Theory

Problem Statement

Managing infrastructure manually across multiple servers leads to configuration drift, inconsistent environments, and time-consuming repetitive tasks. Scaling 

from one server to hundreds becomes nearly impossible with manual SSH-based administration.

What is Ansible?

Ansible is an open-source automation tool for configuration management, application deployment, and orchestration. It follows an agentless architecture — using 

SSH for Linux and WinRM for Windows — and uses YAML-based playbooks to define automation tasks.

Ansible has become the standard choice among enterprise automation solutions because it is simple yet powerful, agentless, community-powered, predictable, and secure.

How Ansible Solves the Problem

<img width="892" height="230" alt="image" src="https://github.com/user-attachments/assets/ae6a0e34-dd3f-4a98-bbae-cc8f8b23466e" />

Key Concepts

<img width="783" height="367" alt="image" src="https://github.com/user-attachments/assets/724dc308-6b4f-4928-aa2e-484f336bfbd6" />

How Ansible Works

Ansible connects from the control node to the managed nodes via SSH, sending commands and instructions. The units of code it executes are called modules. Each module is invoked by a task, and an ordered list of tasks forms a playbook. The managed machines are listed in an inventory file grouped into categories.

```bash
Control Node (Ansible installed)
        │
        │ SSH
        ├──────────── Managed Node 1
        ├──────────── Managed Node 2
        └──────────── Managed Node 3
```

No extra agents required on managed nodes — just a terminal and a text editor to get started.

Part A — Hands-On Lab

Step 1: Install Ansible

Via pip (recommended for macOS/Linux):

```bash
pip install ansible
ansible --version
```

Via apt (Ubuntu/Debian):

```bash
sudo apt update -y
sudo apt install ansible -y
ansible --version
```

Post-installation check:

```bash ansible localhost -m ping ```

Expected output:

```bash
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Step 2: Create SSH Key Pair

Ansible uses SSH key-based authentication to connect to managed nodes without passwords.

```bash
ssh-keygen -t rsa -b 4096
# Accept all defaults — keys saved to ~/.ssh/id_rsa and ~/.ssh/id_rsa.pub
```

Copy keys to the current working directory (needed for building the Docker image):

```bash
cp ~/.ssh/id_rsa.pub .
cp ~/.ssh/id_rsa .
```

Key placement explained:

<img width="1180" height="140" alt="image" src="https://github.com/user-attachments/assets/6381eb9a-02f0-469b-ad13-1625a4f170c8" />

Step 3: Create the Docker Image (Ubuntu SSH Server)

Create a Dockerfile that builds a custom Ubuntu image with OpenSSH pre-configured and our public key baked in:

```bash
FROM ubuntu

RUN apt update -y
RUN apt install -y python3 python3-pip openssh-server
RUN mkdir -p /var/run/sshd

# Configure SSH
RUN mkdir -p /run/sshd && \
    echo 'root:password' | chpasswd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

# Create .ssh directory and set proper permissions
RUN mkdir -p /root/.ssh && \
    chmod 700 /root/.ssh

# Copy SSH keys into the image
COPY id_rsa /root/.ssh/id_rsa
COPY id_rsa.pub /root/.ssh/authorized_keys

# Set proper permissions
RUN chmod 600 /root/.ssh/id_rsa && \
    chmod 644 /root/.ssh/authorized_keys

# Fix for SSH login
RUN sed -i 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd

# Expose SSH port
EXPOSE 22

# Start SSH service
CMD ["/usr/sbin/sshd", "-D"]
```

Build the image:

```bash docker build -t ubuntu-server . ```

<img width="1200" height="767" alt="image" src="https://github.com/user-attachments/assets/1051aa48-543e-4fb6-acad-08f102044647" />

Step 4: Launch 4 Server Containers

```bash
for i in {1..4}; do
    echo -e "\n Creating server${i}\n"
    docker run -d --rm -p 220${i}:22 --name server${i} ubuntu-server
    echo -e "IP of server${i} is $(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' server${i})"
done
```

Expected output:

```bash
Creating server1
IP of server1 is 172.17.0.2

Creating server2
IP of server2 is 172.17.0.3

Creating server3
IP of server3 is 172.17.0.4

Creating server4
IP of server4 is 172.17.0.5
```


Step 5: Create Ansible Inventory

This script auto-generates inventory.ini with the real container IPs:

```bash
echo "[servers]" > inventory.ini
for i in {1..4}; do
    docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' server${i} >> inventory.ini
done

cat << EOF >> inventory.ini

[servers:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
EOF
```

Review the generated file:

```bash cat inventory.ini ```

Expected inventory.ini content:

<img width="1198" height="681" alt="image" src="https://github.com/user-attachments/assets/6a7df640-8828-4529-9e3d-f3814d119c7f" />

Step 6: Test Connectivity

Manual SSH test to confirm keys work:

```bash ssh -i ~/.ssh/id_rsa root@172.17.0.2 ```

Ansible ping test across all servers:

```bash ansible all -i inventory.ini -m ping ```

Expected output:

```bash
172.17.0.2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
172.17.0.3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
172.17.0.4 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
172.17.0.5 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

<img width="1185" height="450" alt="image" src="https://github.com/user-attachments/assets/ec3d6813-7384-4e26-ae9b-9344c4e91fa1" />

For verbose output (useful for debugging):

Step 7: Create and Run Playbook (update.yml)

```bash
---
- name: Update and configure servers
  hosts: all
  become: yes
  tasks:

    - name: Update apt packages
      apt:
        update_cache: yes
        upgrade: dist

    - name: Install required packages
      apt:
        name: ["vim", "htop", "wget"]
        state: present

    - name: Create test file
      copy:
        dest: /root/ansible_test.txt
        content: "Configured by Ansible on {{ inventory_hostname }}"
```

<img width="1196" height="787" alt="image" src="https://github.com/user-attachments/assets/4ccee2ca-a90f-41fe-a368-6960d35e0692" />

Run the playbook:

```bash ansible-playbook -i inventory.ini update.yml ```

Expected output:

```bash
PLAY [Update and configure servers] ********************************************

TASK [Gathering Facts] *********************************************************
ok: [172.17.0.2]
ok: [172.17.0.3]
ok: [172.17.0.4]
ok: [172.17.0.5]

TASK [Update apt packages] *****************************************************
changed: [172.17.0.2]
changed: [172.17.0.3]
...

TASK [Install required packages] ***********************************************
changed: [172.17.0.2]
...

TASK [Create test file] ********************************************************
changed: [172.17.0.2]
...

PLAY RECAP *********************************************************************
172.17.0.2  : ok=4  changed=3  unreachable=0  failed=0
172.17.0.3  : ok=4  changed=3  unreachable=0  failed=0
172.17.0.4  : ok=4  changed=3  unreachable=0  failed=0
172.17.0.5  : ok=4  changed=3  unreachable=0  failed=0
```

<img width="1186" height="378" alt="image" src="https://github.com/user-attachments/assets/5d2725b0-3e47-47d8-a4ee-08915bec60c9" />

Step 8: Create Advanced Playbook (playbook1.yml)

```bash
---
- name: Configure multiple servers
  hosts: servers
  become: yes
  tasks:

    - name: Update apt package index
      apt:
        update_cache: yes

    - name: Install Python 3 (latest available)
      apt:
        name: python3
        state: latest

    - name: Create test file with content
      copy:
        dest: /root/test_file.txt
        content: |
          This is a test file created by Ansible
          Server name: {{ inventory_hostname }}
          Current date: {{ ansible_date_time.date }}

    - name: Display system information
      command: uname -a
      register: uname_output

    - name: Show disk space
      command: df -h
      register: disk_space

    - name: Print results
      debug:
        msg:
          - "System info: {{ uname_output.stdout }}"
          - "Disk space: {{ disk_space.stdout_lines }}"
```

Run it:

```bash ansible-playbook -i inventory.ini playbook1.yml ```

Step 9: Verify Changes

Using Ansible to read the created file across all servers:

 ```bash ansible all -i inventory.ini -m command -a "cat /root/ansible_test.txt" ```

<img width="1182" height="353" alt="image" src="https://github.com/user-attachments/assets/9fefb9b3-bc6d-4832-81fe-8b483804d864" />

Using Docker exec directly:

```bash
for i in {1..4}; do
    docker exec server${i} cat /root/ansible_test.txt
done
```

<img width="1165" height="447" alt="image" src="https://github.com/user-attachments/assets/dc6ccb9c-5c1d-4161-8c7f-2d9fe2c4b116" />

Expected output on each server:

```bash
Configured by Ansible on 172.17.0.2
Configured by Ansible on 172.17.0.3
Configured by Ansible on 172.17.0.4
Configured by Ansible on 172.17.0.5
```

Step 10: Cleanup

Stop all server containers:

```bash
for i in {1..4}; do
    docker rm -f server${i}
done
```

Complete Workflow Summary

```bash
1. Setup SSH keys
        ↓
2. Build ubuntu-server Docker image
        ↓
3. Launch 4 containers (server1–server4)
        ↓
4. Generate inventory.ini with container IPs
        ↓
5. Test connectivity (ansible -m ping)
        ↓
6. Run playbook (ansible-playbook)
        ↓
7. Verify changes
        ↓
8. Cleanup (docker rm -f)
```

Key Takeaways

1.Agentless — Ansible only needs SSH on managed nodes, no extra software to install

2.dempotent — running the same playbook twice produces the same result, no unintended side effects

3. Declarative — you describe what you want (nginx installed, file present), not how to do it step by step

4. Inventory — the single source of truth for all managed nodes, supports groups and variables

5. Modules — 3000+ built-in modules cover everything from apt/yum to AWS/Azure/GCP resources

6. register + debug — capture command output and print it back, useful for auditing and troubleshooting

7. Playbooks scale — the same playbook that ran on 4 Docker containers runs identically on 400 EC2 instances


References

1. Ansible Official Documentation

2. Ansible Tutorial — Spacelift

3. Ansible Official Website

4. Ansible Tower GUI

5. Ansible Tower Tutorial — GeeksforGeeks




 


















































