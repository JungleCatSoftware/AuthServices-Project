# -*- mode: ruby -*-
# vi: set ft=ruby :

def check_plugin(plugin)
  unless Vagrant.has_plugin?("vagrant-#{plugin}")
    raise "Vagrant plugin \"#{plugin}\" is required but not installed. Install with `vagrant plugin install vagrant-#{plugin}`."
  end
end

check_plugin('r10k')

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

  config.r10k.puppet_dir = "puppet"
  config.r10k.puppetfile_path = "Puppetfile"
  config.r10k.module_path = "puppet/modules"

  cache_dir = local_cache(config.vm.box)
  config.vm.synced_folder cache_dir, "/var/cache/apt/archives/"

  config.vm.define :authservicesproxy do |proxy|
    proxy.vm.hostname = "authservicesproxy"
    proxy.vm.network "private_network", ip: "10.10.2.10"
    proxy.vm.network "private_network", ip: "10.10.1.10", virtualbox__intnet: "internal"
    proxy.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "authservicesproxy"
    }
  end

  config.vm.define :authservicesdb do |db|
    db.vm.hostname = "authservicesdb"
    db.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 2
    end
    db.vm.network "private_network", ip: "10.10.1.25", virtualbox__intnet: "internal"
    db.vm.provision "puppet", type: "puppet", facter: {
       "vagrant_nodetype" => "authservicesdb"
    }
  end

  config.vm.define :authservicesweb do |authservicesweb|
    authservicesweb.vm.hostname = "authservicesweb"
    authservicesweb.vm.network "private_network", ip: "10.10.1.15", virtualbox__intnet: "internal"
    authservicesweb.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "authservicesweb"
    }
  end

  config.vm.define :authservicesapi do |authservicesapi|
    authservicesapi.vm.hostname = "authservicesapi"
    authservicesapi.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
    end
    authservicesapi.vm.network "private_network", ip: "10.10.1.16", virtualbox__intnet: "internal"
    authservicesapi.vm.provision "puppet", type: "puppet", facter: {
      "vagrant_nodetype" => "authservicesapi"
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

# vim:ts=2 sts=2 sw=2 et
