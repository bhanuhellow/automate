NODES_NAME = "mistic"
NODES_NUM = 3
MEM_SIZE = 1024
CPU_COUNT = 1
IMAGE_NAME = "generic/alma8"

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  # Loop to create managed nodes
  (1..NODES_NUM).each do |i|
      config.vm.define "node#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.hostname = "#{NODES_NAME}#{i}"
      node.vm.network "public_network", bridge: "eth0: Wi-Fi (AirPort)", ip: "192.168.0.#{i+150}"

      node.vm.provider "virtualbox" do |vb|
        vb.name = "#{NODES_NAME}#{i}"
        vb.memory = "#{MEM_SIZE}"
        vb.cpus = "#{CPU_COUNT}"
      end

      node.vm.provision "file", source: "/etc/hosts", destination: "/tmp/hosts"
      node.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "host_ssh_public_key"
      node.vm.provision "shell", inline: <<-SHELL
        cat host_ssh_public_key >> /home/vagrant/.ssh/authorized_keys
        sudo mv /tmp/hosts /etc/hosts
        sudo chmod 644 /etc/hosts
        sudo sed -i "s/PasswordAuthentication no/PasswordAuthentication yes/g" /etc/ssh/sshd_config && systemctl restart sshd
        echo redhat | passwd vagrant --stdin
      SHELL
    end
  end
end
