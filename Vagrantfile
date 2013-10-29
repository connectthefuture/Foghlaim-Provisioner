# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Capture the current version of Vagrant. We'll use this later
  # to handle backward compatibility with Vagrant 1.2.x
  vagrant_version = Vagrant::VERSION.sub(/^v/, '')

  # A Virtualbox specific setting to set the VM's memory to 512MB.
  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", 512]
  end

  # Forward Agent
  #
  # Enable agent forwarding on vagrant ssh commands. This allows you to use identities
  # established on the host machine inside the guest. See the manual for ssh-add
  config.ssh.forward_agent = true

  # CentOS 6.4, 64 bit release
  #
  # Provides a fairly bare-bones box created by Puppet Labs
  config.vm.box = "centos-64-x64-puppetlabs"
  config.vm.box_url = "http://puppet-vagrant-boxes.puppetlabs.com/centos-64-x64-vbox4210-nocm.box"

  config.vm.hostname = "wp.wsu.edu"
  config.vm.network :private_network, ip: "10.10.30.30"

  # Map a data directory for local use so that the process of checking out the WSUWP Platform
  # only has to occur once every so often.
  if vagrant_version >= "1.3.0"
    config.vm.synced_folder "local", "/var/local", :mount_options => [ "dmode=775", "fmode=774" ]
  else
    config.vm.synced_folder "local", "/var/local", :extra => 'dmode=775,fmode=774'
  end

  # Local Machine Hosts
  #
  # If the Vagrant plugin hostsupdater (https://github.com/cogitatio/vagrant-hostsupdater) is
  # installed, the following will automatically configure your local machine's hosts file to
  # be aware of the domains specified below. Watch the provisioning script as you may be
  # required to enter a password for Vagrant to access your hosts file.
  if defined? VagrantPlugins::HostsUpdater
    config.hostsupdater.aliases = [
    	       "invalid.wp.wsu.edu",
    	       "content.wp.wsu.edu",
    	      "network1.wp.wsu.edu",
      "invalid.network1.wp.wsu.edu",
    	"site1.network1.wp.wsu.edu",
    	"site2.network1.wp.wsu.edu",
    	      "network2.wp.wsu.edu",
      "invalid.network2.wp.wsu.edu",
    	          "school1.wsu.edu",
    	    "site1.school1.wsu.edu",
    	    "site2.school1.wsu.edu",
    	  "invalid.school1.wsu.edu",
    	          "school2.wsu.edu",
    	  "invalid.school2.wsu.edu",
    	          "invalid.wsu.edu"
    ]
  end

  # Salt Provisioning
  #
  # Map the provisioning directory to the guest machine and initiate the provisioning process
  # with salt. On the first build of a virtual machine, if Salt has not yet been installed, it
  # will be bootstrapped automatically.
  config.vm.synced_folder "provision/salt", "/srv/salt"

  config.vm.provision "shell",
    inline: "cp /srv/salt/config/yum.conf /etc/yum.conf"

  config.vm.provision :salt do |salt|
    salt.bootstrap_script = 'provision/bootstrap_salt.sh'
    salt.verbose = true
    salt.minion_config = 'provision/salt/minions/vagrant.conf'
    salt.run_highstate = true
  end
end
