# Vagrant Load Balancer Project

## Introduction

This project sets up a simple environment using Vagrant and VirtualBox:
- One machine (lb) acts as a Load Balancer.
- Three machines (web1, web2, web3) serve a simple React application.

The Load Balancer distributes incoming requests across the web servers.

---

## Requirements

- VirtualBox
- Vagrant
- Node.js and npm

---

## Setup Instructions

1. Open the project folder.
2. Navigate to the frontend folder and install the dependencies:

```
cd frontend
npm install
npm run build
```

3. Return to the main project folder:

```
cd ..
```

4. Start the environment:

```
vagrant up
```

---

## Code Snippets

### Vagrantfile

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "lb" do |lb|
    lb.vm.box = "ubuntu/jammy64"
    lb.vm.hostname = "lb"
    lb.vm.network "private_network", ip: "192.168.70.100"
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
    end
    lb.vm.provision "shell", path: "lb-setup.sh"
  end

  (1..3).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = "ubuntu/jammy64"
      web.vm.hostname = "web#{i}"
      web.vm.network "private_network", ip: "192.168.70.#{100+i}"
      web.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
      end
      web.vm.synced_folder "./frontend/build", "/var/www/tour-app"
      web.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nodejs npm nginx
        cd /vagrant/frontend
        npm install
        npm run build
        rm -rf /var/www/html
        ln -s /var/www/tour-app /var/www/html
      SHELL
    end
  end

end
```

### lb-setup.sh

```bash
#!/bin/bash

sudo apt update
sudo apt install -y nginx

# Configure Nginx Load Balancer
cat <<EOT > /etc/nginx/sites-available/default
upstream backend {
    server 192.168.70.101;
    server 192.168.70.102;
    server 192.168.70.103;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
EOT

sudo systemctl restart nginx
```

---

## Key Decisions Explained

- **Load Balancer (lb):** Using a Load Balancer with Nginx ensures better availability by distributing traffic across multiple servers.
- **Three Web Servers (web1, web2, web3):** Using multiple web servers improves scalability and fault tolerance.
- **Private Networking:** Private IP addresses allow machines to communicate securely inside the virtual network.
- **Frontend Deployment:** React build files are served through Nginx on each web server after the build process.

---

## Testing the Load Balancer

After shutting down web2, multiple requests sent to the Load Balancer IP (http://192.168.70.100) still returned HTTP/1.1 200 OK, confirming that traffic was successfully handled by web1 and web3.

---

## Scaling Commands

- Start a specific server:

```
vagrant up web2
```

- Stop a specific server:

```
vagrant halt web3
```

- Add a new server:
  - Modify the Vagrantfile to add a new machine (e.g., web4).
  - Then run:

```
vagrant up web4
```

---

## Screenshots

The following screenshots are included in the `screenshots/` folder:

- `vagrant-status.png` : Showing the status of all VMs.
- `curl-200ok.png` : Showing HTTP 200 OK response from Load Balancer.
- `frontend-build.png` : Showing the successful build of the React app.
- `nginx-status.png` : Showing Nginx service running on the Load Balancer.
- `load-balancer-test.png` : Showing successful load balancing even after shutting down web2.

---

## Source Acknowledgment

The frontend React project was cloned and adapted from:

https://github.com/cw-barry/Tour-Places-App.git

---

## Shutting Down

To shut down all machines:

```
vagrant halt
```

---

## Done





