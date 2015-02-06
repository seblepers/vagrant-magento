# -*- mode: ruby -*-
# vi: set ft=ruby :

hostname = "vagrant-mage.dev"
virtualbox_ip = "10.0.0.15";

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # VMWare Fusion customization
  config.vm.provider :vmware_fusion do |vmware, override|

    # Which box?
    override.vm.box = "debian-wheezy-fusion"
    override.vm.box_url = "http://boxes.monsieurbiz.com/debian-wheezy-fusion.box"

    # Customize VM
    vmware.vmx["memsize"] = "1024"
    vmware.vmx["numvcpus"] = "1"

    # Network
    vmware.vmx["ethernet1.present"] = "TRUE"
    vmware.vmx["ethernet1.connectionType"] = "hostonly"
    vmware.vmx["ethernet1.virtualDev"] = "e1000"
    vmware.vmx["ethernet1.wakeOnPcktRcv"] = "FALSE"
    vmware.vmx["ethernet1.addressType"] = "generated"

  end

  # Virtualbox customization
  config.vm.provider :virtualbox do |virtualbox, override|

    # Which box?
    override.vm.box = "debian-wheezy"
    override.vm.box_url = "http://boxes.monsieurbiz.com/debian-wheezy.box"

    # Customize VM
    virtualbox.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "1", "--pae", "on", "--hwvirtex", "on", "--ioapic", "on"]

    # Network
    override.vm.network :private_network, ip: virtualbox_ip

  end

  # Network
  config.hostmanager.enabled            = true
  config.hostmanager.manage_host        = true
  config.hostmanager.ignore_private_ip  = false
  config.hostmanager.include_offline    = true
  config.hostmanager.aliases            = "admin." + hostname
  config.vm.hostname                    = hostname

  # Synced folders
  # config.vm.synced_folder "", "/vagrant"
  config.vm.synced_folder "", "/vagrant", nfs: true,
                                    mount_options: ["nolock", "async"]
  # config.vm.synced_folder "htdocs", "/var/www/magento"
  config.vm.synced_folder "htdocs", "/var/www/magento", nfs: true,
                                    mount_options: ["nolock", "async"]

  # "Provision" with hostmanager
  config.vm.provision :hostmanager

  # Puppet!
  config.vm.provision :puppet do |puppet|
    puppet.manifests_path   = "puppet/manifests"
    puppet.module_path      = "puppet/modules"
    puppet.manifest_file    = "init.pp"

    # Factors
    puppet.facter = {
        "vagrant"           => "1",
        "hostname"          => hostname,
        "db_root_password"  => "mysql",
        "db_user"           => "magento",
        "db_password"       => "magento",
        "db_name"           => "magento",
        "db_name_tests"     => "magento_tests",
        "document_root"     => "/var/www/magento",
        "logs_dir"          => "/var/www/logs",
    }
  end

end

# Local file
begin
  load 'LocalVagrantfile.rb'
rescue LoadError
  # ignore
end
