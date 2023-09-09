servers=[
  {:hostname => "k8s-master",:ip => "192.168.10.27",:ssh_port => 2222},
  {:hostname => "k8s-worker1",:ip => "192.168.10.26",:ssh_port => 2223},
  {:hostname => "k8s-worker2",:ip => "192.168.10.25",:ssh_port => 2224},
  {:hostname => "k8s-ingress1",:ip => "192.168.10.24",:ssh_port => 2226},
  {:hostname => "dnsmasq",:ip => "192.168.10.100",:ssh_port => 2227}
]

#last_vm = servers[(servers.length) -1][:hostname]

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 1024
    vb.cpus = 1
    vb.check_guest_additions = false
    config.vm.box_check_update = false
    config.vm.box = "bento/ubuntu-22.04"
  end

  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.hostname = machine[:hostname]
      node.vm.network "public_network", ip: machine[:ip], bridge: "wlo1"
      node.vm.network "forwarded_port", id: "ssh", host: machine[:ssh_port], guest: 22


      if machine[:hostname] == "k8s-master"
        node.vm.provider "virtualbox" do |vb|
          vb.memory = 2048
        end
      end

      if machine[:hostname] == "dnsmasq"
        node.vm.provision "ansible_local" do |ansible|
          ansible.playbook = "ansible/playbook/dnsmasq.yaml"
          ansible.limit = "all,localhost"
        end
      end
    end
  end
end