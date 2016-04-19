# -*- mode: ruby -*-
# vi: set ft=ruby :

def require_plugins(plugins = {})
  needs_restart = false
  plugins.each do |plugin, version|
    next if Vagrant.has_plugin?(plugin)
    cmd =
      [
        'vagrant plugin install',
        plugin
      ]
    cmd << "--plugin-version #{version}" if version
    system(cmd.join(' ')) || exit!
    needs_restart = true
  end
  exit system('vagrant', *ARGV) if needs_restart
end

require_plugins \
  'vagrant-bindfs' => '0.3.2'

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  # Uncomment to use hostmanager plugin
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # On host > ~/.ssh/ssh-add -K
  config.ssh.forward_agent = true

  config.vm.network "forwarded_port", guest: 8888, host: 8888, nic_type: "82545EM"

  # Add to host > ~/.ssh/config
  #   Host 192.168.111.222
  #     ForwardAgent yes
  config.vm.network :private_network, ip: "192.168.111.222"
  config.vm.hostname = "dev.vagrant"

  config.vm.synced_folder ".", "/vagrant", type: "nfs"
  config.bindfs.bind_folder '/vagrant', '/vagrant'

  config.vm.provision "ansible" do |ansible|
    # ansible.verbose = "vvvv"
    ansible.playbook = "provisioning/main.yml"
  end
end
