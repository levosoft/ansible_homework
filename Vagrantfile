Vagrant.configure("2") do |config|

  # Ansible szerver
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/rockylinux-9"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.56.10"
    ansible.vm.synced_folder ".", "/vagrant"
    ansible.vm.synced_folder "./shared", "/shared", type: "virtualbox"

    ansible.vm.provision "shell", inline: <<-SHELL
      # Szükséges csomagok telepítése
      sudo dnf install -y epel-release
      sudo dnf install -y ansible openssh-clients
      
      # SSH kulcs generálása, ha még nincs
      if [ ! -f /home/vagrant/.ssh/id_rsa ]; then
        ssh-keygen -t rsa -b 2048 -f /home/vagrant/.ssh/id_rsa -N ""
      fi

      # Inventory fájl létrehozása
      echo "[web]" > /shared/inventory
      echo "192.168.56.11 ansible_user=homework ansible_ssh_private_key_file=/home/vagrant/.ssh/id_rsa" >> /shared/inventory
    SHELL
  end

  # Web szerver
  config.vm.define "web" do |web|
    web.vm.box = "bento/rockylinux-9"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.56.11"
    web.vm.network "forwarded_port", guest: 80, host: 8888

    web.vm.provision "shell", inline: <<-SHELL
      # Felhasználó létrehozása
      sudo useradd -m homework
      echo "homework:homework" | sudo chpasswd
      echo "homework ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/homework

      # SSH konfiguráció
      sudo mkdir -p /home/homework/.ssh
      sudo chown homework:homework /home/homework/.ssh
      sudo chmod 700 /home/homework/.ssh

      # Az Ansible szerver által generált SSH kulcs átvétele
      echo ">>> Másolás az ansible szerverről..."
    SHELL

    web.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "/home/homework/.ssh/authorized_keys"

    web.vm.provision "shell", inline: <<-SHELL
      sudo chown homework:homework /home/homework/.ssh/authorized_keys
      sudo chmod 600 /home/homework/.ssh/authorized_keys
    SHELL
  end
end
