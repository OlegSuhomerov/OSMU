$script = <<SCRIPT
  docker pull ubuntu
  docker run --name ubuntuContainer -it ubuntu /bin/bash

  if lvs | grep vps; then
  echo "==> SHELL PROVISIONER: Setting up logical volumes"
  lvremove -f vg1

  sudo lvcreate -L 100M -n disk1 local
  sudo lvcreate -L 100M -n disk2 local
  sudo lvcreate -L 100M -n disk3 local
  sudo lvcreate -L 100M -n disk4 local

  sudo mkfs.ext4 /dev/local/disk1
  sudo mkfs.ext4 /dev/local/disk2
  sudo mkfs.ext4 /dev/local/disk3
  sudo mkfs.ext4 /dev/local/disk4

  sudo -s
  sudo apt-get install mdadm
  sudo mdadm --create /dev/md0 --level=mirror --raid-devices=2 /dev/local/disk1 /dev/local/disk2
  sudo mkfs.ext4 /dev/md0
  sudo mkdir /mnt/raid1
  sudo mount /dev/md0 /mnt/raid1
  
  sudo mdadm --create /dev/md1 --level=mirror --raid-devices=2 /dev/local/disk3 /dev/local/disk4
  sudo mkfs.ext4 /dev/md1
  sudo mkdir /mnt/raid2
  sudo mount /dev/md0 /mnt/raid2
  
  mount -a
  sudo visudo -f /etc/sudoers
  %sudo   ALL=(ALL:ALL)NOPASSWD:ALL 
  
  sudo groupadd sampleGroup 
  sudo adduser -m NameSurname 
  sudo usermod -aG sampleGroup,daemon NameSurname -d /etc/
  sudo delgroup sampleGroup 
  SCRIPT

Vagrant.configure("2") do |config|
  
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false

  config.vm.define "machine1" do |machine1|
    machine1.vm.network  "public_network", ip: "192.168.1.1"
    machine1.vm.hostname = "machine1"
    machine1.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory=256
      vb.cpus=1
    end

    ubuntu.vm.provision "shell", 
    inline: "
    sudo apt-get update && sudo apt-get upgrade 
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    
    sudo add-apt-repository 'deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable'
    sudo apt-get update && apt-cache policy docker-ce
    sudo apt-get install -y docker-ce"

    machine1.persistent_storage.enabled = true
    machine1.persistent_storage.location = "disks/sourcehdd.vdi"
    machine1.persistent_storage.size = 500
    machine1.persistent_storage.mount = false
    machine1.persistent_storage.use_lvm = true
    machine1.persistent_storage.format = false
    machine1.persistent_storage.volgroupname = 'vg1'
  end

  config.vm.define "machine2" do |machine2|
    machine2.vm.network "public_network", ip: "192.168.1.2"
    machine2.vm.hostname = "machine2"
    machine2.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory=256
      vb.cpus=1 
    end

    ubuntu.vm.provision "shell", 
      inline: "
      sudo apt-get update && sudo apt-get upgrade 
      sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      
      sudo add-apt-repository 'deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable'
      sudo apt-get update && apt-cache policy docker-ce
      sudo apt-get install -y docker-ce"

    machine2.persistent_storage.enabled = true
    machine2.persistent_storage.location = "disks/sourcehdd1.vdi"
    machine2.persistent_storage.size = 500
    machine2.persistent_storage.mount = false
    machine2.persistent_storage.use_lvm = true
    machine2.persistent_storage.format = false
    machine2.persistent_storage.volgroupname = 'vg1'
  end
       
  config.vm.provision "shell", inline: $script
end