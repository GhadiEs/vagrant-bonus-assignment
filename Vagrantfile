VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  config.vm.define "lb" do |lb|
    lb.vm.box = "ubuntu/jammy64"
    lb.vm.hostname = "lb"
    lb.vm.network "private_network", ip: "192.168.70.100"
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
    end
    lb.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
    SHELL
  end

  (1..3).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = "ubuntu/jammy64"
      web.vm.hostname = "web#{i}"
      web.vm.network "private_network", ip: "192.168.70.#{100+i}"
      web.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
      end
      web.vm.boot_timeout = 600  
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
