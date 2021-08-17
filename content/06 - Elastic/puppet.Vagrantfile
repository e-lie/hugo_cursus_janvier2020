# -*- mode: ruby -*-
# vi: set ft=ruby :

#Config for multinode master/puppet config

IMAGE_NAME = "ubuntu/bionic64"
NODES = {
    "puppet" => IMAGE_NAME,
    "puppet-node1" => IMAGE_NAME,
    "puppet-node2" => IMAGE_NAME
}
ANSIBLE_GROUPS = {
    "puppet-masters" => ["puppet"],
    "puppet-nodes" => ["puppet-node[1:#{NODES.length-1}]"],
}


Vagrant.configure("2") do |puppet6|

    puppet6.vm.provider :virtualbox do |vb|
        vb.customize [
        "modifyvm", :id,
        "--cpuexecutioncap", "100",
        "--memory", "900",
        "--cpus", "1",
        ]

        # Open port 8140

    end
      
    NODES.each_with_index do |(name, image), index|
        puppet6.vm.define "#{name}" do |node|
            node.vm.box = "#{image}"
            node.vm.network "private_network", ip: "192.168.50.#{index + 10}"
            node.vm.network "forwarded_port", guest: 8140, host: "#{index + 8140}"
            node.vm.hostname = "#{name}"

            if index == NODES.length - 1 # After all VMs are created launch parallel provisionning
                puppet6.vm.provision "ansible" do |ansible|
                    ansible.limit = 'all'
                    ansible.playbook = "setup_puppet/main.yml"
                    ansible.groups = ANSIBLE_GROUPS
                end
            end

        end
    end
end