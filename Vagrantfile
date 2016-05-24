# -*- mode: ruby -*-
# vi: set ft=ruby :
def parse_config(
  config_file=File.expand_path(File.join(File.dirname(__FILE__), 'config.yml'))
)
  require 'yaml'
  config = {
    'sites' => "sites",
    'databases' => "databases",
    'memory' => '1024',
    'with_gui' => false,
    'ip' => "192.168.50.4",
    'cpus' => '1',
    'boxname' => 'Parrot',
    'debug' => false,
  }
  if File.exists?(config_file)
    overrides = YAML.load_file(config_file)
    config.merge!(overrides)
  end
  config
end


Vagrant.configure("2") do |config|

    custom_config = parse_config

    config.vm.box = "scotch/box"
    config.vm.network :private_network, ip: custom_config['ip']
    #config.vm.network "private_network", ip: "192.168.33.10"

    # Solr
    config.vm.network :forwarded_port, :guest => 8983, :host => 8983
    # MySQL
    config.vm.network :forwarded_port, :guest => 3306, :host => 3306
    # Varnish
    config.vm.network :forwarded_port, :guest => 80, :host => 8181
    # Apache
    config.vm.network :forwarded_port, :guest => 8080, :host => 8080
    # HTTPS
    config.vm.network :forwarded_port, :guest => 443, :host => 1443
    # Dovecot - IMAP
    config.vm.network :forwarded_port, :guest => 143, :host => 1143
    # Memcache
    config.vm.network :forwarded_port, :guest => 11211, :host => 11211

    config.vm.hostname = "scotchbox"
    #config.vm.synced_folder ".", "/var/www", :mount_options => ["dmode=777", "fmode=666"]

    # Optional NFS. Make sure to remove other synced_folder line too
    #config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    config.vm.synced_folder custom_config['sites'], "/vagrant_sites", :nfs => true
    config.vm.synced_folder custom_config['databases'], "/vagrant_databases"


    # Use Vagrant Cachier
    if Vagrant.has_plugin?("vagrant-cachier")
      config.cache.auto_detect = true
    end

    # Enable ssh key forwarding
    config.ssh.forward_agent = true


    # A quick bootstrap to get Puppet installed.
    config.vm.provision "shell", path: "scripts/bootstrap.sh"

    
    # A quick installation of composer and drush
    config.vm.provision "shell", path: "scripts/drush.sh"



end
