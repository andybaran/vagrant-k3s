default_box = 'bento/ubuntu-18.04'

Vagrant.configure(2) do |config|
  config.vm.define 'master' do |master|
    master.vm.box = default_box
    master.vm.hostname = "master"
    master.vm.synced_folder ".", "/vagrant"
    master.vm.network 'private_network', ip: "192.168.0.200"
    master.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    master.vm.network "forwarded_port", guest: 22, host: 2000 # SSH TO MASTER/NODE
    master.vm.network "forwarded_port", guest: 6443, host: 6443 # ACCESS K8S API
    for p in 30000..30100 # PORTS DEFINED FOR K8S TYPE-NODE-PORT ACCESS
      master.vm.network "forwarded_port", guest: p, host: p, protocol: "tcp"
      end
    master.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "master"
      end

    master.vm.provider "hyperv" do |v|
      v.memory = "1024"
      v.vmname = "master"
      end
man iu
    master.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install etcd-server etcd-client -y
      IPADDR=$(ip -4 address show | grep "inet " | awk '!- /127.0.0.1/ {print $2}')
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8 --write-kubeconfig-mode 644 --kube-apiserver-arg="service-node-port-range=30000-30100" --no-deploy=servicelb --no-deploy=traefik" K3S_STORAGE_BACKEND=etcd3 K3S_STORAGE_ENDPOINT="http://127.0.0.1:2379" sh -
      hostnamectl set-hostname master
      NODE_TOKEN="/var/lib/rancher/k3s/server/node-token"
      while [ ! -e ${NODE_TOKEN} ]
      do
          sleep 1
      done
      cat ${NODE_TOKEN}
      cp ${NODE_TOKEN} /vagrant/
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
      KUBE_CONFIG="/etc/rancher/k3s/k3s.yaml"
      cp ${KUBE_CONFIG} /vagrant/ #copy contents of "k3s.yaml" to ".kube/config" to 'kubectl' from local-machine
    SHELL
  end

  config.vm.define 'node1' do |node1|
    node1.vm.box = default_box
    node1.vm.hostname = "node1"
    node1.vm.synced_folder ".", "/vagrant"

    ### These settings will be ignored by the Hyper-V provider
    node1.vm.network 'private_network', ip: "192.168.0.201"
    node1.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    node1.vm.network "forwarded_port", guest: 22, host: 2001
    ###

    #VirtualBox Config
    node1.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "node1"
      end

    #Hyper-V Config
    node1.vm.provider "hyperv" do |v|
      v.memory = "1024"
      v.vmname = "node1"
      end
    
    node1.vm.provision "shell", inline: <<-SHELL
      IPADDR=$(ip -4 address show | grep "inet " | awk '!- /127.0.0.1/ {print $2}')
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8" K3S_URL=https://192.168.0.200:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
    SHELL
  end

  config.vm.define 'node2' do |node2|
    node2.vm.box = default_box
    node2.vm.hostname = "node2"
    node2.vm.synced_folder ".", "/vagrant"
    
    #### These settings will be ignored by the Hyper-V provider
    node2.vm.network 'private_network', ip: "192.168.0.202"
    node2.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    node2.vm.network "forwarded_port", guest: 22, host: 2002
    ####

    #VirtualBox Config
    node2.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "node2"
      end

    #Hyper-V Config
    node2.vm.provider "hyperv" do |v|
      v.memory = "1024"
      v.vmname = "node2"
      end
    
    node2.vm.provision "shell", inline: <<-SHELL
      IPADDR=$(ip -4 address show | grep "inet " | awk '!- /127.0.0.1/ {print $2}')
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8" K3S_URL=https://192.168.0.200:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      rm -f /vagrant/node-token
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
    SHELL
  end

  config.vm.define 'node3' do |node3|
    node3.vm.box = default_box
    node3.vm.hostname = "node3"
    node3.vm.synced_folder ".", "/vagrant"

    #### These settings will be ignored by the Hyper-V provider
    node3.vm.network 'private_network', ip: "192.168.0.203"
    node3.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    node3.vm.network "forwarded_port", guest: 22, host: 2003
    ####


    #VirtualBox Config
    node3.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "node2"
      end

    #Hyper-V Config
    node3.vm.provider "hyperv" do |v|
      v.memory = "1024"
      v.vmname = "node2"
      end
    
    node3.vm.provision "shell", inline: <<-SHELL
      IPADDR=$(ip -4 address show | grep "inet " | awk '!- /127.0.0.1/ {print $2}')
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8" K3S_URL=https://192.168.0.200:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      rm -f /vagrant/node-token
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
    SHELL
  end

end