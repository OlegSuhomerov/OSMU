Vagrant.configure("2") do |server|

    server.vm.define "mainPk" do |mainPk|
        mainPk.vm.network  "public_network", ip: "192.168.1.1"
        mainPk.vm.hostname = "mainPk"
        mainPk.vm.provider "virtualbox" do |vb|
            vb.gui = true
            vb.memory=1024
            vb.cpus=1
        end
        mainPk.vm.provision "shell",
                        inline: "echo "export PMS_VAR="lolwut"" >> ~/.bashrc && source ~/.bashrc
                        wget https://cdimage.debian.org/debian-cd/current-live/amd64/bt-hybrid/
                        curl -LJO https://github.com/joyent/node/tarball/v0.7.1
                        cat ~/.bashrc | echo $?
                        ssh-keygen -b 1024 -t rsa -f ~/.ssh/genkey
                        ssh-copy-id vagrant@192.168.1.2
                        ssh-copy-id vagrant@192.168.1.3
                        ssh vagrant@192.168.1.2
                        sudo passwd -l root
                        scp vagrant@192.168.1.2:config.txt
                        chmod 777 config.txt
                        tar -cf archive.tar config.txt
                        sudo nano /etc/ssh/sshd_config
                        sudo systemctl restart ssh"
    end
end