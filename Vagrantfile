# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.provider :libvirt do |libvirt|
    libvirt.memory = 1024
    libvirt.nested = true
    libvirt.cpu_mode = "host-model"
    libvirt.random :model => 'random' # Passthrough /dev/random
  end

  config.vm.box = "debian/stretch64"
  config.vm.box_download_checksum = "3625435cbc6ace0a033f64e9495de65286d92d6560dfefe9239a3f9ab02f98a0"
  config.vm.box_download_checksum_type = "sha256"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  hostnames = ['router','desktop','laptop','nas','htpc']
  hostnames.each do |name|
  config.vm.define "#{name}" do |system|
    system.vm.host_name = "#{name}"
    system.vm.provision "ansible" do |ansible|
        ansible.playbook = "#{name}.yml"
        ansible.inventory_path = "inventories/vagrant"
        ansible.limit = "all" # run ansible in parallel for all machines
        ansible.verbose = "vv"
      end
    end
  end

  config.vm.define :router do |router|
    router.vm.network :private_network,
                      :libvirt__network_name => 'ansible_mgmt',
                      :libvirt__dhcp_enabled => false,
                      :ip => "192.168.111.10"
    router.vm.network :private_network,
                      :auto_config => false,
                      :libvirt__forward_mode => 'veryisolated',
                      :libvirt__dhcp_enabled => false,
                      :libvirt__network_name => 'switch_lan'
  end

  config.vm.define :desktop do |desktop|
    desktop.vm.box = "buster.box"
    desktop.vm.network :private_network,
                       :libvirt__network_name => 'ansible_mgmt',
                       :libvirt__dhcp_enabled => false,
                       :ip => "192.168.111.3"
    desktop.vm.network :private_network,
                       :auto_config => false,
                       :libvirt__forward_mode => 'veryisolated',
                       :libvirt__dhcp_enabled => false,
                       :libvirt__network_name => 'switch_lan'
  end

  config.vm.define :laptop do |laptop|
    laptop.vm.network :private_network,
                      :libvirt__network_name => 'ansible_mgmt',
                      :libvirt__dhcp_enabled => false,
                      :ip => "192.168.111.4"
    laptop.vm.network :private_network,
                      :auto_config => false,
                      :libvirt__forward_mode => 'veryisolated',
                      :libvirt__dhcp_enabled => false,
                      :libvirt__network_name => 'switch_wifi'
  end

  config.vm.define :nas do |nas|
    nas.vm.network :private_network,
                   :libvirt__network_name => 'ansible_mgmt',
                   :libvirt__dhcp_enabled => false,
                   :ip => "192.168.111.5"
    nas.vm.network :private_network,
                      :auto_config => false,
                      :libvirt__forward_mode => 'veryisolated',
                      :libvirt__dhcp_enabled => false,
                      :libvirt__network_name => 'switch_lan'
    nas.vm.provider :libvirt do |domain|
      domain.memory = 512
      # 4 disks for raid 10 setup:
      domain.storage :file, :device => 'vdb', :size => '1G', :type => 'raw'
      domain.storage :file, :device => 'vdc', :size => '1G', :type => 'raw'
      domain.storage :file, :device => 'vdd', :size => '1G', :type => 'raw'
      domain.storage :file, :device => 'vde', :size => '1G', :type => 'raw'
    end
  end

  config.vm.define :htpc do |htpc|
    htpc.vm.network :private_network,
                    :libvirt__network_name => 'ansible_mgmt',
                    :libvirt__dhcp_enabled => false,
                    :ip => "192.168.111.6"
    htpc.vm.network :private_network,
                   :auto_config => false,
                   :libvirt__forward_mode => 'veryisolated',
                   :libvirt__dhcp_enabled => false,
                   :libvirt__network_name => 'switch_lan'
  end
end
