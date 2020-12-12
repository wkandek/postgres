#-*- mode: ruby -*-
# vi: set ft=ruby :

################################################################################
# User Variables Section NODE = nodes
################################################################################

VM_SUBNET = "192.168.100."
DP_OCTET = 150
BOX_IMAGE = "ubuntu/bionic64"
NODE_COUNT = 2 
NODECPU = 1 
NODEMEMORY = 2048 

######################################################################################
# VM Configuration Scripts
######################################################################################

$primaryscript = <<-SCRIPT

echo "Setting up ssh..."
sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sed -i -e 's/#PermitRoot/PermitRoot/' /etc/ssh/sshd_config
sed -i -e 's/#PubkeyA/PubkeyA/' /etc/ssh/sshd_config
systemctl reload sshd

mkdir /root/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDDZaeegp+1o4dYElXbAUbk5Yn7diEem3j3oEDqguiQiREAOc4Pq/vDZGfRsMdBBV0DVa3N4f9UlthUR7nfzXWeG/GuZTtQvH2FPUKxpCDHuCwJSmrQfct6J+C+t+lLeygXddtbz96IHh1N6olxku/esApzPkbQ/tqGir2tUJVLqidKkWIkehhRZloGd3wCRzS4xzqnIoN3iuTSoTFTPvywA1T3Kiz527uISo6td30REsOaOQc1WT5EgOO+ZttQ8bxq74G9/y1m4ZPvGTdQCMJFDJw7EdeH8F7zosyAuxkJYjWbTqwxCNisIWGkNO73l7IKCmiTjHKRuU3fgRiksPcN3A2ta2iF+CgB713vDRmtAyamAWknyAmrsu6vAw/qoCyIAwyQCvNntFU/48UfbHrRB79ffUP77EiPFDPmrt2BzjuCy/D/pLuGFYHuQsfyf7OWxCort+JfTxF/2J1ZszOyz7u403DsXvQ9qWWVfHKAuqQ1Cf57kyIKk7bdCpuRPiM= wkandek@wmx1" > /root/.ssh/authorized_keys

echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
apt-get update
apt-get install -y postgresql-13 postgresql-client-13
SCRIPT

$replicascript = <<-SCRIPT

echo "Setting up ssh..."
sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sed -i -e 's/#PermitRoot/PermitRoot/' /etc/ssh/sshd_config
sed -i -e 's/#PubkeyA/PubkeyA/' /etc/ssh/sshd_config
systemctl reload sshd

mkdir /root/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDDZaeegp+1o4dYElXbAUbk5Yn7diEem3j3oEDqguiQiREAOc4Pq/vDZGfRsMdBBV0DVa3N4f9UlthUR7nfzXWeG/GuZTtQvH2FPUKxpCDHuCwJSmrQfct6J+C+t+lLeygXddtbz96IHh1N6olxku/esApzPkbQ/tqGir2tUJVLqidKkWIkehhRZloGd3wCRzS4xzqnIoN3iuTSoTFTPvywA1T3Kiz527uISo6td30REsOaOQc1WT5EgOO+ZttQ8bxq74G9/y1m4ZPvGTdQCMJFDJw7EdeH8F7zosyAuxkJYjWbTqwxCNisIWGkNO73l7IKCmiTjHKRuU3fgRiksPcN3A2ta2iF+CgB713vDRmtAyamAWknyAmrsu6vAw/qoCyIAwyQCvNntFU/48UfbHrRB79ffUP77EiPFDPmrt2BzjuCy/D/pLuGFYHuQsfyf7OWxCort+JfTxF/2J1ZszOyz7u403DsXvQ9qWWVfHKAuqQ1Cf57kyIKk7bdCpuRPiM= wkandek@wmx1" > /root/.ssh/authorized_keys

echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
apt-get update
apt-get install -y postgresql-13 postgresql-client-13
SCRIPT


######################################################################################
# Create VMs Section
######################################################################################

Vagrant.configure("2") do |config|
  config.vm.define "pg-primary" do |node|
    hname = "pg-primary"
    node.vm.box = BOX_IMAGE
    node.vm.hostname = hname
    node.vm.network :private_network, ip: "#{VM_SUBNET}#{DP_OCTET}"
    node.vm.synced_folder "./" , "/vagrant"
    node.vm.provider :virtualbox do |v|
      v.name = hname
      v.memory = NODEMEMORY
      v.cpus = NODECPU
    end
    node.vm.provision "shell" do |s|
      s.inline = $primaryscript
      s.args = hname
    end
  end
  config.vm.define "pg-replica" do |node|
    hname = "pg-replica"
    node.vm.box = BOX_IMAGE
    node.vm.hostname = hname
    node.vm.network :private_network, ip: "#{VM_SUBNET}#{DP_OCTET+1}"
    node.vm.synced_folder "./" , "/vagrant"
    node.vm.provider :virtualbox do |v|
      v.name = hname
      v.memory = NODEMEMORY
      v.cpus = NODECPU
    end
    node.vm.provision "shell" do |s|
      s.inline = $replicascript
      s.args = hname
    end
  end
end
