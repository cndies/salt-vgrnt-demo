# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  os = "ubuntu/xenial64"
  net_ip = "192.168.50"

  config.vm.define :master, primary: true do |master_config|
    master_config.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = 1
        vb.name = "master"
    end
      master_config.vm.box = "#{os}"
      master_config.vm.host_name = 'saltmaster.vgrnt'
      master_config.vm.network "private_network", ip: "#{net_ip}.10"
      # master_config.vm.synced_folder "saltstack/salt/", "/srv/salt"
      # master_config.vm.synced_folder "saltstack/pillar/", "/srv/pillar"

      master_config.vm.provision :salt do |salt|
        salt.master_config = "saltstack/etc/master"
        salt.master_key = "saltstack/keys/master_minion.pem"
        salt.master_pub = "saltstack/keys/master_minion.pub"
        salt.minion_key = "saltstack/keys/master_minion.pem"
        salt.minion_pub = "saltstack/keys/master_minion.pub"
        salt.seed_master = {
                            "minion-test.vgrnt" => "saltstack/keys/minion.pub",
                            "minion-prod.vgrnt" => "saltstack/keys/minion.pub"
                           }

        salt.install_type = "stable"
        salt.install_master = true
        salt.no_minion = true
        salt.verbose = true
        salt.colorize = true
        salt.bootstrap_options = "-P -c /tmp"
      end
  end

    [
      ["minion",   "test",   "#{net_ip}.11",    "512",    os ],
      ["minion",   "prod",   "#{net_ip}.21",    "512",    os ],
    ].each do |vmname, env, ip, mem, os|
      config.vm.define "#{vmname}-#{env}" do |minion_config|
        minion_config.vm.provider "virtualbox" do |vb|
            vb.memory = "#{mem}"
            vb.cpus = 1
            vb.name = "#{vmname}-#{env}"
        end
        minion_config.vm.box = "#{os}"
        minion_config.vm.hostname = "#{vmname}-#{env}.vgrnt"
        minion_config.vm.network "private_network", ip: "#{ip}"

        minion_config.vm.provision :salt do |salt|
          salt.minion_config = "saltstack/etc/#{vmname}-#{env}"
          salt.minion_key = "saltstack/keys/#{vmname}.pem"
          salt.minion_pub = "saltstack/keys/#{vmname}.pub"
          salt.install_type = "stable"
          salt.verbose = true
          salt.colorize = true
          salt.bootstrap_options = "-P -c /tmp"
        end
      end
    end
end
