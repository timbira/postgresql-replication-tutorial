# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$scriptbase = <<SCRIPT
yum -y install https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
SCRIPT

$scriptclient = $scriptbase + <<SCRIPTCLIENT
yum -y install vim postgresql10

exit 0
SCRIPTCLIENT

$scriptserver = $scriptbase + <<SCRIPTSERVER
yum -y install vim postgresql10-server postgresql10-contrib
systemctl enable postgresql-10
/usr/pgsql-10/bin/postgresql-10-setup initdb
systemctl start postgresql-10

exit 0
SCRIPTSERVER

$addhosts = <<ADDHOSTS
cat >> /etc/hosts <<EOF
10.10.10.10 app.local
10.10.10.11 db1.local
10.10.10.12 db2.local
10.10.10.13 db3.local
10.10.10.14 db4.local
10.10.10.15 db5.local
10.10.10.16 db6.local
EOF
ADDHOSTS

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	3.times do |n|
		if n == 0 then
			vm_name = "app"
		else
			vm_name = "db" + n.to_s
		end
		config.vm.define vm_name do |cc|
			cc.vm.box = "minimal/centos7"
			cc.vm.box_version = "7.0"
			if n == 0 then
				cc.vm.hostname = "app"
			else
				cc.vm.hostname = "db" + n.to_s
			end
			cc.vm.network :private_network, ip: "10.10.10.1" + n.to_s
			cc.vm.provider :virtualbox do |vb|
				vb.customize ["modifyvm", :id, "--memory", "512"]
				vb.customize ["modifyvm", :id, "--usb", "off"]
				vb.customize ["modifyvm", :id, "--usbcardreader", "off"]
			end
			cc.vm.provision "shell", inline: $addhosts, privileged: true
			if vm_name == "app" then
				cc.vm.provision "shell", inline: $scriptclient, privileged: true, name: "install-postgresql-client"
			else
				cc.vm.provision "shell", inline: $scriptserver, privileged: true, name: "install-postgresql-server"
			end
		end
	end
end
