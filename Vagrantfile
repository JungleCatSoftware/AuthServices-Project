# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2" 

# Create local caching for packages
def local_cache(box_name)
  cache_dir = File.join(File.dirname(__FILE__), '.vagrant_cache', 'apt', box_name)
  partial_dir = File.join(cache_dir, 'partial')
  FileUtils.mkdir_p(partial_dir) unless File.exists? partial_dir
  cache_dir
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  # Work around cosmetic issue (default uses -l which misbehaves when not in interactive shell)
  config.ssh.shell = "/bin/bash"

  config.librarian_puppet.puppetfile_dir = "puppet"
  config.librarian_puppet.destructive = true

  cache_dir = local_cache(config.vm.box)
  config.vm.synced_folder cache_dir, "/var/cache/apt/archives/"

  config.vm.define :webproxy do |web|
    web.vm.network "forwarded_port", guest: 80, host: 8080
    web.vm.network "private_network", ip: "10.10.1.10", virtualbox__intnet: "internal"
    web.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "webproxy"
    }
  end

  config.vm.define :accounts do |accounts|
    accounts.vm.network "private_network", ip: "10.10.1.15", virtualbox__intnet: "internal"
    accounts.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "accounts"
    }
  end

  config.vm.define :accountsapi do |accountsapi|
    accountsapi.vm.network "private_network", ip: "10.10.1.16", virtualbox__intnet: "internal"
    accountsapi.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "accountsapi"
    }
  end

  config.vm.provision "puppet", type: "puppet" do |puppet|
    puppet.module_path = "puppet/modules"
    puppet.manifests_path = "puppet/manifests"
    puppet.hiera_config_path = "puppet/hiera.yaml"
    puppet.working_directory = "/vagrant/puppet"
    puppet.facter = {
      "vagrant" => true
    }
  end

end
