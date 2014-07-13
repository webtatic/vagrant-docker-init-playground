def docker_vm(main_config, options=nil)
  defaults = {
    :roles => [],
    :build_dir => options[:name],
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
      if options[:image]
        docker.image = options[:image]
      else 
        docker.build_dir = "./#{options[:build_dir]}"
      end
      docker.ports = options[:ports] if options[:ports]
      docker.has_ssh = true
      docker.remains_running = true

      create_args = ['--name', options[:name]]

      # These init systems need CAP_SYS_ADMIN
      create_args += ['--privileged']

      docker.create_args = create_args
    end
  end
end

Vagrant.configure("2") do |main_config|

  main_config.vm.define 'docker' do |config|
    config.vm.hostname = "docker.dev"
    config.vm.box = 'fedora-20'
    config.vm.box_url = 'http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_fedora-20_chef-provisionerless.box'
    config.vm.synced_folder ".", "/vagrant"

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
    :init => :sysvinit

  docker_vm main_config,
    :name => 'centos7',
    :init => :systemd

  docker_vm main_config,
    :name => 'fedora20',
    :init => :systemd
end
