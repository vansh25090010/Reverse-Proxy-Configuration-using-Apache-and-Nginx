# SRE Assignment – 1

Reverse Proxy Configuration using Apache and Nginx

---

## 1. Objective

The objective of this assignment is to configure password-less SSH authentication between three Ubuntu virtual machines and to implement reverse proxy functionality using Apache and Nginx web servers.

---

## 2. Environment Details

Operating System: Ubuntu 24.04.3 LTS
Hypervisor: VMware
Number of Virtual Machines: 3

### VM Specifications

Each virtual machine was allocated the following resources:

* RAM: 4 GB
* CPU: 2 Cores
* Disk: 20 GB
* Network Mode: NAT / Host-Only

### Virtual Machine Details

| VM Name | Hostname | Username | IP Address      |
| ------- | -------- | -------- | --------------- |
| VM1     | vm1      | machine1 | 192.168.198.128 |
| VM2     | vm2      | machine2 | 192.168.198.129 |
| VM3     | vm3      | machine3 | 192.168.198.130 |

---

## 3. Configure Password-less SSH Authentication Between All VMs

### Install OpenSSH Server

Run the following commands on all virtual machines:

```bash
sudo apt update
sudo apt install openssh-server -y
```

### Enable and Start SSH Service

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

### Generate SSH Keys

```bash
ssh-keygen
```

### Copy SSH Keys Between VMs

From VM1:

```bash
ssh-copy-id machine2@192.168.198.129
ssh-copy-id machine3@192.168.198.130
```

From VM2:

```bash
ssh-copy-id machine1@192.168.198.128
ssh-copy-id machine3@192.168.198.130
```

From VM3:

```bash
ssh-copy-id machine1@192.168.198.128
ssh-copy-id machine2@192.168.198.129
```

This configuration enables password-less SSH login between all virtual machines.

---

## 4. Install Apache Web Server on All VMs

### Apache Installation

```bash
sudo apt update
sudo apt install apache2 -y
```

### Create Test HTML Pages

* VM1: This is VM1
* VM2: This is VM2
* VM3: This is VM3

Example:

```bash
echo "<h1>This is VM1</h1>" | sudo tee /var/www/html/index.html
```

Apache was verified by accessing each VM’s IP address in a web browser.

---

## 5. Configure Reverse Proxy Using Apache

### Enable Apache Proxy Modules

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo systemctl restart apache2
```

### Example Apache Reverse Proxy Configuration (VM1)

Edit the Apache default configuration file:

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

```apache
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    ProxyPass /app1 http://192.168.198.128/
    ProxyPassReverse /app1 http://192.168.198.128/

</VirtualHost>
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

## 6. Install and Configure Nginx Reverse Proxy

### Install Nginx

```bash
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

### Edit Default Nginx Configuration

```bash
sudo nano /etc/nginx/sites-available/default
```

### Example Nginx Reverse Proxy Configuration

Inside the `server { }` block:

```nginx
location /app1/ {
    proxy_pass http://192.168.198.128/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

Similar configurations were applied on VM2 and VM3 for their respective backend services.

---

## 7. Verification

* Password-less SSH login verified between all virtual machines
* Apache reverse proxy routing tested successfully
* Nginx reverse proxy routing verified using browser access

---

## 8. Conclusion

This assignment successfully demonstrates password-less SSH authentication and reverse proxy configuration using both Apache and Nginx. The implementation reflects real-world SRE practices such as service isolation, proxy routing, and multi-server communication.

---

## License

This project is for academic and learning purposes only.
