
$mach_quant = STDIN.gets.chomp

if $mach_quant.to_i > 253 && 
    $mach_quant.to_i < 1 then

    puts "Please enter a valid value!"
else

    Vagrant.configure("2") do |server|

        server.vm.provider "virtualbox" do |vb|
            vb.gui = true
            vb.memory=256
            vb.cpus=1
            vb.check_guest_additions=false

            server.vm.box_check_update=false
            server.vm.box="ubuntu/trusty64"
        end

        (1..$mach_quant.to_i).each do |i|
            server.vm.define "node№#{i}" do |node|
                node.vm.network "public_network", ip: "192.168.1.#{i}"
                node.vm.hostname = "node№#{i}"

                if i==1 then

                    node.vm.provision "shell",
                        inline: "sudo apt install nginx -y"
                else

                    node.vm.provision "shell", inline: <<-SHELL
                        add-apt-repository ppa:openjdk-r/ppa -y
                        apt-get update
                        apt-get -y install apache2 openjdk-8-jdk
                        update-alternatives --config java
                        groupadd tomcat
                        useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
                        wget -q http://mirrors.gigenet.com/apache/tomcat/tomcat-8/v8.0.30/bin/apache-tomcat-8.0.30.tar.gz
                        mkdir /opt/tomcat
                        tar xvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
                        chgrp -R tomcat /opt/tomcat/conf
                        chmod g+rwx /opt/tomcat/conf
                        chmod g+r /opt/tomcat/conf/*
                        chown -R tomcat /opt/tomcat/work/ /opt/tomcat/temp/ /opt/tomcat/logs/
                        wget -q https://gist.githubusercontent.com/adeubank/1a8db1958578146120a2/raw/c796a8881d95dd2cbe47d1ffee8246463ec2c7a1/tomcat.conf
                        sudo mv tomcat.conf /etc/init/
                        sed -i "s#</tomcat-users>#  <user username=\\"admin\\" password=\\"secret\\" roles=\\"manager-gui,admin-gui\\"/>\\n</tomcat-users>#" /opt/tomcat/conf/tomcat-users.xml
                        initctl reload-configuration
                        initctl start tomcat
                      SHELL
                end

                (1..$mach_quant.to_i).each do |j|
                       node.vm.provision "shell", inline: "echo '192.168.1.#{j}    node№#{j}' > /etc/hosts"
                end
            end
        end
    end
end