Vagrant.configure("2") do |config|
    config.vm.provider :virtualbox do |v|
      v.memory = 8192
      v.cpus = 4
    end
  
    config.vm.define :master do |master|
      master.vm.box = "ubuntu/focal64"
      master.vm.hostname = "master"
      master.vm.network :private_network, ip: "10.12.0.10"
      master.vm.provision :shell, privileged: false, inline: <<-SHELL
      sudo apt update && sudo apt install -y wireguard
      sudo cp /vagrant/wg1.conf /etc/wireguard/wg1.conf
      sudo systemctl enable wg-quick@wg1
      sudo systemctl start wg-quick@wg1
      curl -sfL https://get.k3s.io | sh -s - --disable=traefik --node-name=vagrant-k3s
      sudo cp /etc/rancher/k3s/k3s.yaml /vagrant/k3s.yaml
  SHELL
    end
  end
 