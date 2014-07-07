def docker_vm(main_config, options=nil)
  defaults = {
    :roles => [],
    :hostname => "#{options[:name]}.dev"
  }

  options ||= {}
  options = defaults.merge(options)

  main_config.vm.define options[:name] do |config|
    config.vm.hostname = options[:hostname]
    config.vm.synced_folder ".", "/vagrant"

    config.vm.provider :docker do |docker|
      docker.vagrant_vagrantfile = __FILE__
      docker.vagrant_machine = 'docker'
      docker.build_dir = "./#{options[:box]}"
      docker.has_ssh = true
      docker.remains_running = true
      docker.create_args = ['--privileged']
    end
  end
end

Vagrant.configure("2") do |main_config|

  main_config.vm.define 'docker' do |config|
    config.vm.hostname = "docker.dev"
    config.vm.box = 'fedora-20'
    config.vm.box_url = 'http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_fedora-20_chef-provisionerless.box'
    config.vm.synced_folder ".", "/vagrant", :disabled => true

    config.vm.provider :virtualbox do |vbox|
      vbox.name = "docker-fedora-20"
    end

    $script = <<SCRIPT
yum -y install docker-io
systemctl enable docker.service
systemctl start docker.service
usermod -a -G docker vagrant
SCRIPT

    config.vm.provision "shell", inline: $script

  end

  docker_vm main_config,
    :name => 'centos6',
    :box => 'centos-6'

  docker_vm main_config,
    :name => 'centos7',
    :box => 'centos-7'

  docker_vm main_config,
    :name => 'fedora20',
    :box => 'fedora-20'
end
