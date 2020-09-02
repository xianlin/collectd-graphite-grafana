GRAPHITE_SERVER_IP="192.168.33.101"
CLIENT_IP="192.168.33.102"

Vagrant.configure('2') do |config|
  config.vm.define :server do |server|
    server.vm.box = 'bento/centos-7.4'
    server.vm.hostname = 'server.example.local'
    server.vm.network :private_network, ip: GRAPHITE_SERVER_IP, 
      virtualbox__intnet: 'intnet'
    server.vm.network "forwarded_port", guest: 3000, host: 3000
    server.vm.network "forwarded_port", guest: 80, host: 8080
    server.vm.provider 'virtualbox' do |vb|
      vb.cpus = '2'
      vb.memory = '1024'
      vb.name = 'server'
      vb.customize ['modifyvm', :id, '--vram', '8']
      vb.customize ['modifyvm', :id, '--audio', 'none']
    end
    server.vm.provision :shell do |s|
      s.inline = <<-SHELL
        yum install -y epel-release
        yum install -y python3 python3-pip
        pip3 install --upgrade pip3
        pip3 install ansible
      SHELL
    end
    server.vm.provision :ansible_local do |ansible|
      ansible.playbook = "collectd_server.yml"
    end
  end

  config.vm.define :client do |client|
    client.vm.box = 'bento/centos-7.4'
    client.vm.hostname = 'client.example.local'
    client.vm.network :private_network, ip: CLIENT_IP,
      virtualbox__intnet: 'intnet'
    client.vm.provider 'virtualbox' do |vb|
      vb.cpus = '2'
      vb.memory = '1024'
      vb.name = 'client'
      vb.customize ['modifyvm', :id, '--vram', '8']
      vb.customize ['modifyvm', :id, '--audio', 'none']
    end
      client.vm.provision :shell do |s|
        s.inline = <<-SHELL
          yum install -y epel-release
          yum install -y python3 python3-pip
          pip3 install --upgrade pip3
          pip3 install ansible
        SHELL
      end
    client.vm.provision :ansible_local do |ansible|
      ansible.extra_vars = { GRAPHITE_HOST: GRAPHITE_SERVER_IP }
      ansible.playbook = "collectd_client.yml"
    end
  end
end
