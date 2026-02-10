# Setting Up Ansible Lab with Vagrant on Windows 11

## Prerequisites
- Windows 11 with virtualization enabled
- Administrator access

## Step 1: Install VirtualBox
1. Download from [virtualbox.org](https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html)
2. Run the installer and follow the setup wizard
3. Restart your computer

## Step 2: Install Vagrant
1. Download from [vagrantup.com](https://www.vagrantup.com/downloads)
2. Run the installer
3. Restart your computer
4. Verify installation: `vagrant --version`

## Step 3: Create Vagrant Project Directory
```bash
mkdir c:\Users\Lenovo\Documents\setup-ansible-lab-vagrant
cd c:\Users\Lenovo\Documents\setup-ansible-lab-vagrant
```

## Step 4: Add a Rocky Linux 9 Vagrant Box (VirtualBox)
Rocky provides official Vagrant box builds. Add the VirtualBox box:
```bash
vagrant box add rocky9-vbox https://dl.rockylinux.org/pub/rocky/9/images/x86_64/Rocky-9-Vagrant-Vbox.latest.x86_64.box
```
Verify:
```bash
vagrant box list
```

## Step 5: Initialize Vagrant Configuration
In the directory create early, create a file call **Vagrantfile** (Note: make sure to don't have extenxion .txt and other)
Create a `Vagrantfile`:
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "rocky9-vbox"

  # 3 nodes with static private IPs (easy for Ansible/K8s labs)
  NODES = [
    { name: "rhel-master",  ip: "192.168.56.10", cpu: 2, mem: 3072 },
    { name: "rhel-worker1", ip: "192.168.56.11", cpu: 2, mem: 2048 },
    { name: "rhel-worker2", ip: "192.168.56.12", cpu: 2, mem: 2048 }
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
```

## Step 6: Deploy Nodes
```bash
vagrant up --provider=virtualbox
```

## Step 7: Verify Setup
```bash
vagrant status
vagrant ssh rhel-master
```

## Useful Commands
- `vagrant up` - Start all nodes
- `vagrant halt` - Stop all nodes
- `vagrant destroy` - Delete all nodes
- `vagrant ssh nodeX` - Connect to specific node
