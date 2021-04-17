Vagrant.configure("2") do |server|

    server.vm.define "pk1" do |pk1|
        pk1.vm.network  "public_network", ip: "192.168.1.2"
        pk1.vm.hostname = "pk1"
        pk1.vm.provider "virtualbox" do |vb|
            vb.gui = true
            vb.memory=256
            vb.cpus=1
        end
        pk1.vm.provision "shell",
                inline: "ssh-keygen -b 256 -t rsa -f ~/.ssh/genkey
                        ssh-copy-id vagrant@192.168.1.1"
        end
    server.vm.define "pk2" do |pk2|
        pk2.vm.network  "public_network", ip: "192.168.1.3"
        pk2.vm.hostname = "pk2"
        pk2.vm.provider "virtualbox" do |vb|
            vb.gui = true
            vb.memory=512
            vb.cpus=1
        end
        pk2.vm.provision "shell",
                    inline: "
                    ssh-keygen -b 512 -t rsa -f ~/.ssh/genkey
                    ssh-copy-id vagrant@192.168.1.1"

  end
  
    server.vm.define "mainPk" do |mainPk|
        mainPk.vm.network  "public_network", ip: "192.168.1.1"
        mainPk.vm.hostname = "mainPk"
        mainPk.vm.provider "virtualbox" do |vb|
            vb.gui = true
            vb.memory=1024
            vb.cpus=1
        end
        mainPk.vm.provision "shell",
                    inline: "
                    ssh-keygen -b 1024 -t rsa -f ~/.ssh/genkey
                    ssh-copy-id vagrant@192.168.1.2
                    ssh-copy-id vagrant@192.168.1.3
                    ssh vagrant@192.168.1.2
                    sudo passwd -l root
                    scp vagrant@192.168.1.2:config.txt
                    chmod 777 config.txt
                    tar -cf archive.tar config.txt    
                    sudo nano /etc/ssh/sshd_config
                    sudo systemctl restart ssh
                    "
        mainPk.vm.provision "shell",
                    inline: "ssh vagrant@192.168.1.3
                    sudo passwd -l root
                    scp vagrant@192.168.1.3:config.txt
                    chmod 777 config.txt
                    tar -cf archive.tar config.txt    
                    sudo nano /etc/ssh/sshd_config
                    sudo systemctl restart ssh"
    end 
end