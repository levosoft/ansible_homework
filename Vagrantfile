Vagrant.configure("2") do |config|

  # Ansible szerver
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/rockylinux-9"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.56.10"

    ansible.vm.provision "shell", inline: <<-SHELL
      sudo dnf install -y epel-release
      sudo dnf install -y ansible
      echo "[web]" > /shared/inventory
      echo "192.168.56.11 ansible_user=homework ansible_ssh_private_key_file=/shared/web_private_key" >> /shared/inventory
      ssh-keyscan -H 192.168.56.11 >> ~/.ssh/known_hosts
    SHELL

    ansible.vm.synced_folder ".", "/vagrant"
    ansible.vm.synced_folder "./shared", "/shared", type: "virtualbox"
  end

  # Web szerver
  config.vm.define "web" do |web|
    web.vm.box = "bento/rockylinux-9"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.56.11"
    web.vm.network "forwarded_port", guest: 80, host: 8888

    web.vm.provision "shell", inline: <<-SHELL
      sudo useradd -m homework
      echo "homework:homework" | sudo chpasswd
      echo "homework ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/homework

      # SSH kulcs generálása
      ssh-keygen -t rsa -b 2048 -f /home/vagrant/web_private_key -q -N ""
      
      # Privát kulcs másolása a megosztott mappába
      sudo cp /home/vagrant/web_private_key /shared/web_private_key
      sudo chmod 600 /shared/web_private_key

      # Publikus kulcs hozzáadása a homework userhez
      sudo mkdir -p /home/homework/.ssh
      sudo chmod 700 /home/homework/.ssh
      sudo cp /home/vagrant/web_private_key.pub /home/homework/.ssh/authorized_keys
      sudo chmod 600 /home/homework/.ssh/authorized_keys
      sudo chown -R homework:homework /home/homework/.ssh
    SHELL

    web.vm.synced_folder "./shared", "/shared"
  end
end
