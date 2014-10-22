Vagrant.configure('2') do |config|
  # Prepare berks
  [:up, :provision, :destroy].each do |cmd|
    config.trigger.before cmd, stdout: true, force: true, vm: /^configserver$/ do
      run 'rm -f Berksfile.lock'
      run 'berks install'
      run 'pkill -f chef-zero'
    end
  end

  # Install Chef
  config.omnibus.chef_version = :latest
  config.omnibus.install_url = 'http://www.opscode.com/chef/install.sh'

  # Chef-Zero
  config.chef_zero.enabled = true

  config.vm.define 'configserver' do |cs|
    # Set hostname
    cs.vm.hostname = 'configserver'

    # Every Vagrant virtual environment requires a box to build off of
    cs.vm.box = 'chef/centos-6.5'

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system
    cs.vm.box_url = 'chef/centos-6.5'

    cs.vm.network :private_network, ip: '33.33.33.40'

    # Enabling the Berkshelf plugin
    cs.berkshelf.enabled = true

    # Chef run to create things
    cs.vm.provision :chef_client do |chef|
      chef.json = {
        'mongodb' => {
          'configserver_url' => '33.33.33.40'
        }
      }

      chef.add_recipe 'role-mongodb-configserver::default'
    end
  end

  config.vm.define 'mongos' do |mongos|
    # Set hostname
    mongos.vm.hostname = 'mongos'

    # Every Vagrant virtual environment requires a box to build off of
    mongos.vm.box = 'chef/centos-6.5'

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system
    mongos.vm.box_url = 'chef/centos-6.5'

    mongos.vm.network :private_network, ip: '33.33.33.41'

    # Enabling the Berkshelf plugin
    mongos.berkshelf.enabled = true

    # Chef run to create things
    mongos.vm.provision :chef_client do |chef|
      chef.add_recipe 'role-mongodb-mongos::default'
    end
  end
end
