# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  config.disksize.size = '25GB'  
  config.vm.define 'ubuntu18' do |ubuntu18|
    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    ubuntu18.vm.box = "ubuntu/bionic64"
    ubuntu18.vm.network "private_network", ip: "172.28.128.250"
    ubuntu18.vm.hostname = "ubuntu18"
    ubuntu18.ssh.forward_agent = true
#    ubuntu18.vm.network "forwarded_port", guest: 3000, host: 3000, host_ip: "127.0.0.1" # theia
#    ubuntu18.vm.network "forwarded_port", guest: 8080, host: 8080, host_ip: "127.0.0.1" # http alt
#    ubuntu18.vm.network "forwarded_port", guest: 9080, host: 9080, host_ip: "127.0.0.1" # websphere profile
#    ubuntu18.vm.network "forwarded_port", guest: 9043, host: 9043, host_ip: "127.0.0.1" # websphere console
#    ubuntu18.vm.network "forwarded_port", guest: 7001, host: 7001, host_ip: "127.0.0.1" # websphere console
#    ubuntu18.vm.network "forwarded_port", guest: 9002, host: 9002, host_ip: "127.0.0.1" # websphere console
#    ubuntu18.vm.network "forwarded_port", guest: 1521, host: 1521, host_ip: "127.0.0.1" # oracle listener
#    ubuntu18.vm.network "forwarded_port", guest: 5500, host: 5500, host_ip: "127.0.0.1" # oracle oem
#    ubuntu18.vm.network "forwarded_port", guest: 1414, host: 1414, host_ip: "127.0.0.1" # ibm mq
#    ubuntu18.vm.network "forwarded_port", guest: 9443, host: 9443, host_ip: "127.0.0.1" # ibm mq console
#    ubuntu18.vm.network "forwarded_port", guest: 8081, host: 8081, host_ip: "127.0.0.1" # amlbox
#    ubuntu18.vm.network "forwarded_port", guest: 8443, host: 8443, host_ip: "127.0.0.1" # amlbox console
#    ubuntu18.vm.network "forwarded_port", guest: 5000, host: 5000, host_ip: "127.0.0.1" # docker registry
#    ubuntu18.vm.network "forwarded_port", guest: 6443, host: 6443, host_ip: "127.0.0.1" # kubernetes api server
#    ubuntu18.vm.network "forwarded_port", guest: 8401, host: 8401, host_ip: "127.0.0.1" # swserver
    if Vagrant.has_plugin?("vagrant-vbguest")
      config.vbguest.auto_update = true  
    end
	
	config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
	end
	
	config.vm.synced_folder "opt/eastnets/", "/opt/eastnets"

    # Create a public network, which generally matched to bridged network.
    # Bridged networks make the machine appear as another physical device on
    # your network.
    # config.vm.network "public_network"

    # Share an additional folder to the guest VM. The first argument is
    # the path on the host to the actual folder. The second argument is
    # the path on the guest to mount the folder. And the optional third
    # argument is a set of non-required options.
    # config.vm.synced_folder "../data", "/vagrant_data"
    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:

    ubuntu18.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      # Customize the amount of memory on the VM:
#      vb.memory = "16384"
      vb.memory = "8192"
      vb.name = "Demo-Profiling-Radar360-KYC"
    end

    # View the documentation for the provider you are using for more
    # information on available options.

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
    # config.vm.provision "shell", inline: <<-SHELL
    #   apt-get update
    #   apt-get install -y apache2
    # SHELL
    #
    # As Offical way dosn't work
    # login to docker
    ubuntu18.vm.provision "docker" do |d|	 
	print "----------------------------------------------\n"
	print " Eastnets Demo\n"
	print "----------------------------------------------\n"
	print " Please enter your dockerhub login informations\n"
	print "----------------------------------------------\n"
	print " Dockerid : "
	dockerid = STDIN.gets.chomp
	print " Password : "
	dockerpwd = STDIN.gets.chomp
	  d.post_install_provision "shell", inline:<<-SHELL
	    docker kill portainer
		docker rm portainer
		docker kill eastnetsdemo-profiling_radar360_kyc
		docker rm eastnetsdemo-profiling_radar360_kyc
		docker login -u '#{dockerid}' -p '#{dockerpwd}'
	  SHELL
      d.run "portainer/portainer",
		args:" -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data"
      d.pull_images "eastnetsdemo/profiling_radar360_kyc"
      d.run "eastnetsdemo/profiling_radar360_kyc",
        args:" -v /opt/eastnets:/opt/eastnets --ulimit nofile=50000:50000 -p 1433:1433 -p 8080:8080 -p 8999:8999 -p 9002:9002 -p 8483:8483"
    end
  end

end
