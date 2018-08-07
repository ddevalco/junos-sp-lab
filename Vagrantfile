# -*- mode: ruby -*-
# vi: set ft=ruby :

# Check required plugins
REQUIRED_PLUGINS = %w(vagrant-junos vagrant-host-shell)
exit unless REQUIRED_PLUGINS.all? do |plugin|
  Vagrant.has_plugin?(plugin) || (
    puts "The #{plugin} plugin is required. Please install it with:"
    puts "$ vagrant plugin install #{plugin}"
    false
  )
end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

# This section is where the topology is defined. Vagrant will define what
# the topology will look like. The file give instructions to VirtualBox
# on how all the vSRXs are connected. This is how the physical environment 
# will be set up. Additionally resources are allocated to each VM 

# Provder Router 1
  config.vm.define "p1" do |p1|
    p1.vm.network "private_network", virtualbox__intnet: "net1" # P1 - PE1
    p1.vm.network "private_network", virtualbox__intnet: "net4" # P1 - RR1
    p1.vm.network "private_network", virtualbox__intnet: "net5" # P1 - P2
    p1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      p1.vm.provider :virtualbox do |vb|
        vb.name = "P1"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Router 2
  config.vm.define "p2" do |p2|
    p2.vm.network "private_network", virtualbox__intnet: "net5" # P2 - P1
    p2.vm.network "private_network", virtualbox__intnet: "net3" # P2 - PE2
    p2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      p2.vm.provider :virtualbox do |vb|
        vb.name = "P2"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 1
  config.vm.define "pe1" do |pe1|
    pe1.vm.network "private_network", virtualbox__intnet: "net1" # PE1 - P1
    pe1.vm.network "private_network", virtualbox__intnet: "net2" # PE1 - PE2
    pe1.vm.network "private_network", virtualbox__intnet: "net9" # PE1 - CE1   
    pe1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe1.vm.provider :virtualbox do |vb|
        vb.name = "PE1"
        vb.memory = 1024
        vb.gui = false
      end
  end

# Provder Edge 2
  config.vm.define "pe2" do |pe2|
    pe2.vm.network "private_network", virtualbox__intnet: "net3" # PE2 - P2
    pe2.vm.network "private_network", virtualbox__intnet: "net2" # PE2 - PE1
    pe2.vm.network "private_network", virtualbox__intnet: "net12" # PE2 - CE2
    pe2.vm.network "private_network", virtualbox__intnet: "net13" # PE2 - CE3 
    pe2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      pe2.vm.provider :virtualbox do |vb|
        vb.name = "PE2"
        vb.memory = 1024
        vb.gui = false
      end
  end



# Route Reflector 1
  config.vm.define "rr1" do |rr1|
    rr1.vm.network "private_network", virtualbox__intnet: "net4" # RR1 - P1
    rr1.vm.network "private_network", virtualbox__intnet: "net28" # RR1 - Ansible
    rr1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      rr1.vm.provider :virtualbox do |vb|
        vb.name = "RR1"
        vb.memory = 512
        vb.gui = false
      end
  end

# Customer Edge 1
  config.vm.define "ce1" do |ce1|
    ce1.vm.network "private_network", virtualbox__intnet: "net9" # CE1 - PE2
    ce1.vm.network "private_network", virtualbox__intnet: "net10" # CE1 - EH1
    ce1.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce1.vm.provider :virtualbox do |vb|
        vb.name = "CE1"
        vb.memory = 512
        vb.gui = false
      end
  end


# Customer Edge 2
  config.vm.define "ce2" do |ce2|
    ce2.vm.network "private_network", virtualbox__intnet: "net12" # CE2 - PE2
    ce2.vm.network "private_network", virtualbox__intnet: "net14" # CE2 - EH3
    ce2.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce2.vm.provider :virtualbox do |vb|
        vb.name = "CE2"
        vb.memory = 512
        vb.gui = false
      end
  end

# Customer Edge 3
  config.vm.define "ce3" do |ce3|
    ce3.vm.network "private_network", virtualbox__intnet: "net13" # CE3 - PE2
    ce3.vm.network "private_network", virtualbox__intnet: "net15" # CE3 - CE4
    ce3.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
      ce3.vm.provider :virtualbox do |vb|
        vb.name = "CE3"
        vb.memory = 512
        vb.gui = false
      end
  end

# Server 1 
    config.vm.define "eh1" do |eh1|
        eh1.vm.box = "ubuntu/trusty64"
        eh1.vm.hostname = "eh1"
        eh1.vm.network 'private_network', ip: "192.168.10.20", :netmask => "255.255.255.128", virtualbox__intnet: "net10" # EH1 - CE1
        eh1.ssh.insert_key = true
          eh1.vm.provider :virtualbox do |vb|
            vb.name = "eh1"
            vb.memory = 512
            vb.gui = false
          end
        eh1.vm.provision "shell", inline: <<-SHELL
	          sudo ip route add 192.168.0.0/16 via 192.168.10.1 dev eth1
            sudo ip route add 224.0.0.0/4 via 192.168.10.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 3 
    config.vm.define "eh3" do |eh3|
        eh3.vm.box = "ubuntu/trusty64"
        eh3.vm.hostname = "eh3"
        eh3.vm.network 'private_network', ip: "192.168.20.20", virtualbox__intnet: "net14" # EH3 - CE2
        eh3.ssh.insert_key = true
          eh3.vm.provider :virtualbox do |vb|
            vb.name = "eh3"
            vb.memory = 512
            vb.gui = false
          end
        eh3.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.20.1 dev eth1
            sudo ip route add 224.0.0.0/4 via 192.168.20.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Server 4  
    config.vm.define "eh4" do |eh4|
        eh4.vm.box = "ubuntu/trusty64"
        eh4.vm.hostname = "eh4"
        eh4.vm.network 'private_network', ip: "192.168.30.20", virtualbox__intnet: "net15" # EH4 - CE3
        eh4.ssh.insert_key = true
          eh4.vm.provider :virtualbox do |vb|
            vb.name = "eh4"
            vb.memory = 512
            vb.gui = false
          end
        eh4.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.30.1 dev eth1
            sudo ip route add 224.0.0.0/4 via 192.168.30.1 dev eth1
            sudo apt-get install lldpd iperf mtr -y
          SHELL
    end

# Ansible / Naplam Host
    config.vm.define "naplam" do |naplam|
        naplam.vm.box = "ubuntu/xenial64"
        naplam.vm.hostname = "ansible"
        naplam.vm.network 'private_network', ip: "192.168.1.51", :netmask => "255.255.255.254", virtualbox__intnet: "net28" # Ansible - RR1
        naplam.ssh.insert_key = true
          naplam.vm.provider :virtualbox do |vb|
            vb.name = "naplam"
            vb.memory = 512
            vb.gui = false
          end
        naplam.vm.provision "shell", inline: <<-SHELL
            sudo ip route add 192.168.0.0/16 via 192.168.1.50 dev enp0s8
            sudo apt-get update && sudo apt-get upgrade -y
            sudo apt-get install -y build-essential libssl-dev libffi-dev python-dev libxml2-dev libxslt-dev python-pip python-dev
            sudo pip install --upgrade pip
            sudo pip install setuptools --upgrade
            sudo pip install junos-eznc napalm ansible napalm-ansible
          SHELL
        end  

# This is where the Ansible Groups and Playbook will be defined so when the
# the topology is built configuration will be automated and each vSRX will 
# havre the correct base confirguration

  config.vm.provision "ansible" do |ansible|
    ansible.groups = {
      "base" => [
	"p1", 
	"p2", 
	"pe1", 
	"pe2",
	"rr1"
	],
      "p_routers" => [
	"p1", 
	"p2"
	],
      "pe_routers" => [
	"pe1", 
	"pe2"
	],
      "rr_router" => [
	"rr1"
	],
      "ce_routers" => [
  "ce1",
  "ce2",
  "ce3"
        ]
    }
    ansible.playbook = "junos-sp.yml"
#    ansible.verbose = "vvv"
  end
end
