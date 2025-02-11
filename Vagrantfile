# Vagrantfile
Vagrant.configure("2") do |config|

  # Ansible szerver
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/rockylinux-9"
    ansible.vm.hostname = "ansible"
    ansible.vm.provision "shell", inline: <<-SHELL
      sudo dnf install -y epel-release
      sudo dnf install -y ansible
    SHELL
  end

  # Web szerver
  config.vm.define "web" do |web|
    web.vm.box = "bento/rockylinux-9"
    web.vm.hostname = "web"
    web.vm.network "forwarded_port", guest: 80, host: 8888
    web.vm.provision "shell", inline: <<-SHELL
      sudo useradd -m homework
      echo "homework:homework" | sudo chpasswd
      echo "homework ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/homework
    SHELL
  end
end