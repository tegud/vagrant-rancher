$rancherserver_ip = "172.22.101.100"
$rancherserver_node_ip_base = "172.22.101.10"

$install_docker = <<INSTALL_DOCKER
echo Installing docker
#curl -sSL https://get.docker.com | sed 's/apt-get install -y -q docker-engine/apt-get install -y -q docker-engine=1.10.3-0~trusty/g' | sh -
#curl -sSL https://get.docker.com | sh -
curl -sSL --retry 5 --retry-delay 10 https://releases.rancher.com/install-docker/1.12.6.sh| sh
sudo usermod -aG docker ubuntu

INSTALL_DOCKER

$install_rancher = <<INSTALL_RANCHER
echo Installing rancher
sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server

INSTALL_RANCHER

$join_cluster = <<JOIN_CLUSTER
apt-get install -y -q jq
curl 'http://172.22.101.100:8080/v2-beta/settings/api.host' -X PUT -H 'content-type: application/json' -H 'x-api-no-challenge: true'  --data-binary '{"id":"api.host","type":"activeSetting","baseType":"setting","name":"api.host","activeValue":null,"inDb":false,"source":null,"value":"http://172.22.101.100:8080"}'
sleep 10
curl -sSL --retry 5 --retry-delay 10 http://172.22.101.100:8080/v2-beta/projects/1a5/registrationtoken | jq --raw-output .data[0].command | sh

JOIN_CLUSTER

Vagrant.configure("2") do |config|

  config.vm.provision "shell", inline: $install_docker

  config.vm.define "rancherserver" do |rancherserver|
    rancherserver.vm.hostname = 'rancherserver'
    rancherserver.vm.box= "ubuntu/trusty64"
    rancherserver.vm.box_url = "ubuntu/trusty64"

    rancherserver.vm.network :private_network, ip: $rancherserver_ip, nic_type: "82545EM"

    rancherserver.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", 1042]
      v.customize ["modifyvm", :id, "--name", "rancherserver"]
    end

    rancherserver.vm.provision "shell", inline: $install_rancher
  end

  (1..2).each do |i|
    config.vm.define "ranchernode-#{i}" do |node|
      node.vm.hostname = "ranchernode-#{i}"
      node.vm.box= "ubuntu/trusty64"
      node.vm.box_url = "ubuntu/trusty64"

      node.vm.network :private_network, ip: "#{$rancherserver_node_ip_base}#{i}", nic_type: "82545EM"

      node.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", 2048]
        v.customize ["modifyvm", :id, "--name", "ranchernode-#{i}"]
      end

      node.vm.provision "shell", inline: $join_cluster
    end
  end
end
