# -*- mode: ruby -*-
# vi: set ft=ruby :

$instance_name = "dev"
$vm_memory = 4048
$vm_cpus = 2
$shared_folders = {'~/www' => '/www'}

$setup = <<SCRIPT
  chown vagrant:vagrant /home/vagrant/.ssh/gitwrap.sh
  chmod +x /home/vagrant/.ssh/gitwrap.sh
  chmod 600 /home/vagrant/.ssh/id_rsa
  chmod 600 /home/vagrant/.ssh/id_rsa_work
  sudo yum -y install epel-release
  sudo yum repolist
  sudo yum -y update
  sudo yum -y install curl wget nmap vim gcc make git rsync bzip2 traceroute
  sudo yum clean all &> /dev/null
  git config --global user.email "daniele.moraschi@gmail.com"
  git config --global user.name "Daniele Moraschi"
  git config --global push.default simple
  git config --global color.ui true
  git config --global core.fileMode false
SCRIPT

$docker = <<SCRIPT
  sudo yum -y remove docker docker-selinux
  echo $'[dockerrepo]\nname=Docker Repository\nbaseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/\nenabled=1\ngpgcheck=1\ngpgkey=https://yum.dockerproject.org/gpg\n' \
    | sudo tee --append /etc/yum.repos.d/docker.repo > /dev/null
  sudo yum -y install docker-engine
  sudo service docker start
  sudo chkconfig docker on
  sudo curl -L https://github.com/docker/compose/releases/download/1.7.0/docker-compose-`uname -s`-`uname -m` > docker-compose
  sudo mv docker-compose /bin/docker-compose
  sudo chmod +x /bin/docker-compose
SCRIPT

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "centos/7"
  #config.ssh.insert_key = false
  #config.vm.box = "minimal/centos7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network :private_network, ip: "192.168.10.10"
  config.vm.hostname = $instance_name

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "~/www", "/www", type: "nfs"
  $shared_folders.each_with_index do |(host_folder, guest_folder), index|
    config.vm.synced_folder host_folder.to_s, guest_folder.to_s, id: "%s-share-%02d" % [$instance_name, index], type: "nfs"
  end

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #   # Customize the amount of memory on the VM:
      vb.memory = $vm_memory
      vb.cpus = $vm_cpus
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "file", source: "~/.profile", destination: "~/.bashrc"
  config.vm.provision "file", source: "~/.ssh/gitwrap.sh", destination: "~/.ssh/gitwrap.sh"
  config.vm.provision "file", source: "~/.ssh/id_rsa", destination: "~/.ssh/id_rsa"
  config.vm.provision "file", source: "~/.ssh/id_rsa_work", destination: "~/.ssh/id_rsa_work"
  config.vm.provision "shell", inline: $setup
  config.vm.provision "shell", inline: $docker
end
