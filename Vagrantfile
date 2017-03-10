
$install_ovs = <<SCRIPT
apt-get update
apt-get install -y openvswitch-switch
SCRIPT

SERVER1_SUBNET_1_IP      = "192.168.1.10"
SERVER1_MGMT_IP           = "192.168.101.10"

SERVER2_SUBNET_2_IP      = "192.168.2.10"
SERVER2_MGMT_IP          = "192.168.101.20"

ROUTER_MGMT_IP           = "192.168.101.254"
ROUTER_SUBNET_1_GATEWAY = "192.168.1.254"
ROUTER_SUBNET_2_GATEWAY = "192.168.2.254"

Vagrant.configure(2) do |config|
   config.vm.box = "rboyer/ubuntu-trusty64-libvirt"
   config.vm.synced_folder '.', '/vagrant', disabled: true
   
   config.vm.provider :libvirt do |domain|
      domain.memory = 2048
      domain.cpus = 2
      domain.nested = true
      domain.volume_cache = 'none'
   end

   # "ROUTER"
   config.vm.define "router" do |server|
      server.vm.hostname = "router"
      server.vm.network :public_network,
        :dev => "phy1",
        :mode => "bridge",
        :type => "bridge",
        :ovs  => true,
        :auto_config => false

      server.vm.network :public_network,
        :dev => "phy2",
        :mode => "bridge",
        :type => "bridge",
        :ovs  => true,
        :auto_config => false
     
       #Bring up interface
       server.vm.provision "shell", inline: "ifconfig eth1 up"
       server.vm.provision "shell", inline: "ifconfig eth2 up"
       server.vm.provision "shell", inline: "ifconfig eth1 #{ROUTER_SUBNET_1_GATEWAY} netmask 255.255.255.0"
       server.vm.provision "shell", inline: "ifconfig eth2 #{ROUTER_SUBNET_2_GATEWAY} netmask 255.255.255.0"

       server.vm.provision "shell", inline: $install_ovs 
   end

   #SERVER-1
    config.vm.define "server1" do |server|
      server.vm.hostname = "server1"
      server.vm.network :public_network,
        :dev => "phy1",
        :mode => "bridge",
        :type => "bridge",
        :ovs  => true,
        :auto_config => false
       
       #Bring up interface
       server.vm.provision "shell", inline: "ifconfig eth1 up"
       server.vm.provision "shell", inline: "ifconfig eth1 #{SERVER1_SUBNET_1_IP} netmask 255.255.255.0"

       server.vm.provision "shell", inline: $install_ovs 
   end
  #SERVER-2
    config.vm.define "server2" do |server|
      server.vm.hostname = "server2"
      server.vm.network :public_network,
        :dev => "phy2",
        :mode => "bridge",
        :type => "bridge",
        :ovs  => true,
        :auto_config => false

       #Bring up interface
       server.vm.provision "shell", inline: "ifconfig eth1 up"
       server.vm.provision "shell", inline: "ifconfig eth1 #{SERVER2_SUBNET_2_IP} netmask 255.255.255.0"

       server.vm.provision "shell", inline: $install_ovs 
   end


end  


