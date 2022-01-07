$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.define "mysqldb" do |mysql|
    mysql.vm.network "public_network", ip: "192.168.2.199"
    mysql.vm.provision "shell", inline: $script_mysql
    mysql.vm.provision "shell",
      inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
    mysql.vm.provision "shell", inline: "service mysql restart"

    mysql.vm.synced_folder "./configs", "/configs"
    mysql.vm.synced_folder ".", "/vagrant", disabled: true
  end

  config.vm.define "phpweb" do |phpweb|
    phpweb.vm.network "forwarded_port", guest: 8888, host: 1234
    phpweb.vm.network "public_network", ip: "192.168.2.198"

    phpweb.vm.provision "shell",
      inline: "apt-get update && apt-get install -y puppet"

    phpweb.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "./configs/manifests"
      puppet.manifest_file = "phpweb.pp"
    end
  end
  config.vm.define "mysqlserver" do |mysqlserver|

    mysqlserver.vm.network "public_network", ip:"192.168.2.197" 
  
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.network "public_network", ip:"192.168.2.196" 
    ansible.vm.provision "shell",
      inline: "apt-get update && \
       apt-get install -y software-properties-common  && \
       add-apt-repository --yes --update ppa:ansible/ansible  && \
       apt-get install -y ansible "
  end



end