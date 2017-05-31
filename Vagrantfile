# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  if /cygwin|mswin|mingw|bccwin|wince|emx/ =~ RUBY_PLATFORM
    puts("Configuring for windows")
    config.vm.synced_folder "../..", "/cord", mount_options: ["dmode=700,fmode=600"]
    Box = "ubuntu/xenial64"
    Provider = "virtualbox"
  elsif RUBY_PLATFORM =~ /linux/
    puts("Configuring for linux")
    config.vm.synced_folder "../..", "/cord", type: "nfs"
    Box = "ubuntu1604"
    Provider = "libvirt"
  else
    puts("Configuring for other")
    config.vm.synced_folder "../..", "/cord"
    Box = "ubuntu/xenial64"
    Provider = "virtualbox"
  end

  config.vm.define "voltha" do |d|
    d.ssh.forward_agent = true
    d.vm.box = Box
    d.vm.hostname = "voltha"
    d.vm.network "private_network", ip: "10.100.198.220"
    d.vm.provision :shell, path: "ansible/scripts/bootstrap_ansible.sh"
    d.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /cord/incubator/voltha/ansible/voltha.yml -c local"
    d.vm.provision :shell, inline: "cd /cord/incubator/voltha && source env.sh && make install-protoc && chmod 777 /tmp/fluentd"
    d.vm.provider Provider do |v|
      v.memory = 6144
    end
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

end
