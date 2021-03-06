# -*- mode: ruby -*-
# vi: set ft=ruby :

if ENV['no_proxy'] != nil or ENV['NO_PROXY']
  $no_proxy = ENV['NO_PROXY'] || ENV['no_proxy'] || "127.0.0.1,localhost"
  $subnet = "192.168.121"
  # NOTE: This range is based on vagrant-libivirt network definition
  (1..27).each do |i|
    $no_proxy += ",#{$subnet}.#{i}"
  end
end

Vagrant.configure("2") do |config|
  config.vm.box = "elastic/ubuntu-16.04-x86_64"

  if ENV['http_proxy'] != nil and ENV['https_proxy'] != nil
    if not Vagrant.has_plugin?('vagrant-proxyconf')
      system 'vagrant plugin install vagrant-proxyconf'
      raise 'vagrant-proxyconf was installed but it requires to execute again'
    end
    config.proxy.http     = ENV['http_proxy'] || ENV['HTTP_PROXY'] || ""
    config.proxy.https    = ENV['https_proxy'] || ENV['HTTPS_PROXY'] || ""
    config.proxy.no_proxy = $no_proxy
  end

  config.vm.provider 'libvirt' do |v|
    v.nested = true
    v.cpu_mode = 'host-passthrough'
  end

  config.vm.define :packetgen do |packetgen|
    packetgen.vm.hostname = "packetgen"
    packetgen.vm.provision 'shell', path: 'packetgen'
    packetgen.vm.network :private_network, :ip => "192.168.10.2", :type => :static # unprotected_private_net_cidr
    packetgen.vm.network :private_network, :ip => "10.10.12.2", :type => :static, :netmask => "16" # onap_private_net_cidr
  end	
  config.vm.define :firewall do |firewall|
    firewall.vm.hostname = "firewall"
    firewall.vm.provision 'shell', path: 'firewall'
    firewall.vm.network :private_network, :ip => "192.168.10.3", :type => :static # unprotected_private_net_cidr
    firewall.vm.network :private_network, :ip => "192.168.20.3", :type => :static # protected_private_net_cidr
    firewall.vm.network :private_network, :ip => "10.10.12.3", :type => :static, :netmask => "16" # onap_private_net_cidr
  end
  config.vm.define :sink do |sink|
    sink.vm.hostname = "sink"
    sink.vm.provision 'shell', path: 'sink'
    sink.vm.network :private_network, :ip => "192.168.20.4", :type => :static # protected_private_net_cidr
    sink.vm.network :private_network, :ip => "10.10.12.4", :type => :static, :netmask => "16" # onap_private_net_cidr
  end
end
