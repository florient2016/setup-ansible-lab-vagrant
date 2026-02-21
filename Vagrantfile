Vagrant.configure("2") do |config|
  config.vm.box = "almalinux/9"

  config.vm.boot_timeout = 600
  config.ssh.timeout = 300

  # 3 nodes with static private IPs (easy for Ansible/K8s labs)
  NODES = [
    { name: "rhel-master",  ip: "192.168.56.10", cpu: 1, mem: 1072 },
    { name: "rhel-worker1", ip: "192.168.56.11", cpu: 1, mem: 1048 },
    { name: "rhel-worker2", ip: "192.168.56.12", cpu: 1, mem: 1048 }
  ]

  NODES.each do |n|
    config.vm.define n[:name] do |node|
      node.vm.hostname = n[:name]
      node.vm.network "private_network", ip: n[:ip]

      node.vm.provider "virtualbox" do |vb|
        vb.name = n[:name]
        vb.cpus = n[:cpu]
        vb.memory = n[:mem]
      end

      # Optional: minimal provisioning for labs
      node.vm.provision "shell", inline: <<-SHELL
        set -euxo pipefail
        dnf -y update || true
        dnf -y install vim git curl wget net-tools || true
      SHELL
    end
  end
end