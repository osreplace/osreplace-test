# -*- mode: ruby -*-
# vi: set ft=ruby :

def configure(c, ip: nil)
  c.vm.network 'private_network', ip: ip if ip

  c.vm.provider 'virtualbox' do |vb|
    vb.memory = 512
  end

  osreplace_path = '../osreplace' if File.directory?('../osreplace')
  c.vm.synced_folder osreplace_path, '/opt/osreplace' if osreplace_path
end

def aptget_upgrade(c)
  c.vm.provision 'shell', inline: <<-SHELL
    sudo apt-get update && sudo apt-get -y upgrade
  SHELL
end

def apt_based_vm(config, name, box, options = {})
  config.vm.define name.to_s do |c|
    c.vm.box = box
    configure(c, **options)
    aptget_upgrade(c)
    run_tests(c)
    yield c if block_given?
    c
  end
end

VMTESTROOT = '/vagrant'.freeze

def run_tests(c)
  c.vm.provision 'shell', inline: <<-SHELL
    #{VMTESTROOT}/bin/bootstrap
  SHELL
end

Vagrant.configure(2) do |config|
  apt_based_vm(config, 'debian', 'bento/debian-8.2')
  apt_based_vm(config, 'ubuntu', 'bento/ubuntu-16.04')
  apt_based_vm(config, 'ubuntu-trusty', 'bento/ubuntu-14.04')
end
