# Vagrantfile to create an Ubuntu VM on Platform9 OpenStack
# In our environment we have to boot from a new volume rather
# than booting directly from an image, hence the `volume_boot`
# config hash.

$script = <<SCRIPT
wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
sudo dpkg -i puppet6-release-bionic.deb
sudo apt-get update

sudo apt-get install puppet-agent
sudo apt-get install puppet-bolt

SCRIPT

Vagrant.configure("2") do |config|
  config.ssh.username = 'ubuntu'
  config.ssh.private_key_path = ENV['OS_SSH_KEY_PATH']
  config.ssh.insert_key = true
  config.vm.provider :openstack do |os|
    os.openstack_auth_url   = ENV['OS_AUTH_URL']
    os.identity_api_version = ENV['OS_IDENTITY_API_VERSION']
    os.username             = ENV['OS_USERNAME']
    os.password             = ENV['OS_PASSWORD']
    os.keypair_name         = ENV['OS_KEYPAIR_NAME']
    os.project_name         = ENV['OS_PROJECT_NAME']
    os.region               = ENV['OS_REGION_NAME']
    os.domain_name          = ENV['OS_PROJECT_DOMAIN_ID']
    os.flavor               = 'vol.small'
    os.floating_ip_pool     = 'external'
    os.networks             = ['network1']
    os.security_groups      = ['default']
    os.server_name          = 'slicejump'
# Comment out availability_zone 'nova' if currently unavailable. Will default to 'local-storage'
#    os.availability_zone    = 'nova'
    os.volume_boot          = {
      size:              15,
      delete_on_destroy: true,
      image:             'ubuntu_18.04_server_x86_64',
    }
  end

  # KILL SMB WITH FIRE
  config.vm.allowed_synced_folder_types = [:rsync]

  config.vm.provision "shell" do |s|
    s.inline = $script
  end

  config.vm.provision "puppet" do |puppet|
    puppet.environment      = 'production'
    puppet.environment_path = 'puppet/environments'
  end

end
