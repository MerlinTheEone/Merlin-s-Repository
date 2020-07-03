# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.ssh.insert_key = false

  config.vm.define "slave" do |slave|
    slave.vm.box="shekeriev/centos-8-minimal"
    slave.vm.hostname = "slave.dob.lab"
    slave.vm.network "private_network", ip: "192.168.99.101"
    slave.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct: true
	slave.vm.network "forwarded_port", guest: 80, host: 80, auto_correct: true
    slave.vm.synced_folder "vagrant/slave/", "/vagrant"
  end

  config.vm.define "master" do |master|
    master.vm.box="shekeriev/centos-8-minimal"
    master.vm.hostname = "master.dob.lab"
    master.vm.network "private_network", ip: "192.168.99.100"
    master.vm.synced_folder "vagrant/master/", "/vagrant"
    master.vm.provision "shell", inline: <<EOS
echo "* Add hosts ..."
echo "192.168.99.100 master.dob.lab master" >> /etc/hosts
echo "192.168.99.101 slave.dob.lab slave" >> /etc/hosts

echo "* Install Ansible ..."
dnf install -y epel-release
dnf install -y ansible

echo "* Set Ansible configuration in /etc/ansible/ansible.cfg ..."
cp /vagrant/ansible.cfg /etc/ansible/ansible.cfg

echo "* Set Ansible global inventory in /etc/ansible/hosts ..."
cp /vagrant/hosts /etc/ansible/hosts

echo "* Copy Ansible playbooks in /playbooks/ ..."
cp -R /vagrant/playbooks /playbooks

echo "* Prepare /playbooks/roles folder ..."
cp -R /vagrant/roles /playbooks

echo "* Install Ansible role(s) for jenkins and docker in /playbooks/roles/ ..."
ansible-galaxy install geerlingguy.jenkins -p /playbooks/roles/
ansible-galaxy install geerlingguy.docker -p /playbooks/roles/
ansible-galaxy install geerlingguy.java -p /playbooks/roles/

echo "* Execute Ansible Playbooks ..."
ansible-playbook /playbooks/install-all.yml
EOS
  end
end
 
