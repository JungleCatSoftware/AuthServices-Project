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

#if ['up', 'provision'].include?(ARGV[0])
#  # Run Librarian-Puppet
#  #system 'which', 'bash'
#  #system 'which', 'ruby'
#  #puts ENV['PATH']
#  system '/usr/bin/ruby', '/var/lib/gems/2.1.0/gems/librarian-puppet-2.2.1/bin/librarian-puppet', 'install', '--clean', '--path=./puppet/modules'
#  exit 1
#end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  # Work around cosmetic issue (default uses -l which misbehaves when not in interactive shell)
  config.ssh.shell = "/bin/bash"

  config.librarian_puppet.puppetfile_dir = "puppet"
  config.librarian_puppet.destructive = true

  cache_dir = local_cache(config.vm.box)
  config.vm.synced_folder cache_dir, "/var/cache/apt/archives/"

  config.vm.define :test do |test|
  end

  config.vm.provision :puppet do |puppet|
    puppet.module_path = "puppet/modules"
    puppet.manifests_path = "puppet/manifests"
    puppet.hiera_config_path = "puppet/hiera.yaml"
    puppet.working_directory = "/vagrant/puppet"
  end

end
