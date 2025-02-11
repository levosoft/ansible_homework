Vagrant.configure("2") do |config|
  # Hosts fájl frissítése mindkét gépen
  $etchosts = <<-SCRIPT
    echo '192.168.56.10 ansible' >> /etc/hosts
    echo '192.168.56.11 web' >> /etc/hosts
  SCRIPT

  # Ansible szerver
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/rockylinux-9"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.56.10"
    ansible.vm.synced_folder ".", "/vagrant"
    ansible.vm.synced_folder "./shared", "/shared", type: "virtualbox"
    
    ansible.vm.provision "shell", inline: <<-SHELL
      sudo dnf install -y epel-release
      sudo dnf install -y ansible
      sudo ssh-keygen -t rsa -b 2048 -f /home/vagrant/.ssh/id_rsa -N ""
      sudo cp /home/vagrant/.ssh/id_rsa.pub /vagrant/shared/id_rsa.pub
      sudo chown vagrant:vagrant /home/vagrant/.ssh/id_rsa*
      sudo chmod 600 /home/vagrant/.ssh/id_rsa
      echo "[web]" > /shared/inventory
      echo "192.168.56.11 ansible_user=ansible" >> /shared/inventory
    SHELL
  end

  # Web szerver
  config.vm.define "web" do |web|
    web.vm.box = "bento/rockylinux-9"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.56.11"
    web.vm.network "forwarded_port", guest: 80, host: 8888
    web.vm.synced_folder "./shared", "/shared", type: "virtualbox"
    
    web.vm.provision "shell", inline: <<-SHELL
      sudo useradd -m -s /bin/bash ansible
      echo "ansible:ansible" | sudo chpasswd
      echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
      sudo mkdir -p /home/ansible/.ssh
      sudo cp /shared/id_rsa.pub /home/ansible/.ssh/authorized_keys
      sudo chown -R ansible:ansible /home/ansible/.ssh
      sudo chmod 600 /home/ansible/.ssh/authorized_keys
    SHELL
  end
end
