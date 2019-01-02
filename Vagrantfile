# -*- mode: ruby -*-
# vi: set ft=ruby :

$initiatescript = <<SCRIPT
echo "Installing dependencies ..."
echo "Download Teleport installation"
sudo cp /mnt/teleport-v3.1.1-linux-amd64-bin.tar.gz ~/

echo "Install teleport"
cd ~/
tar -xzf teleport-v3.1.1-linux-amd64-bin.tar.gz

echo "Installing Teleport version ${TELEPORT_DEMO_VERSION} ..."
cd ~/teleport
sudo ./install

echo "Build Teleport data"
sudo mkdir -p /var/lib/teleport

SCRIPT

$teleportserver = <<SCRIPT
echo "Enable teleport systemd"
cd ~/teleport/examples/systemd
sudo cp teleport.service /etc/systemd/system/teleport.service

echo "Reload and start Teleport systemd"
sudo systemctl daemon-reload
sudo systemctl enable teleport
sudo systemctl start teleport
sleep 5

echo "Create certificate for securing node from untrusted auth servers"
sudo tctl auth export --type=tls > /mnt/ca.cert

echo "Generate pub key for teleport"
sudo tctl auth export > /mnt/auth.pub
SCRIPT

$teleportclient = <<SCRIPT
sudo mkdir -p /var/lib/teleport
sudo cp /mnt/ca.cert /var/lib/teleport/ca.cert
sudo cp /mnt/auth.pub /var/lib/teleport/auth.pub
SCRIPT

# Specify a Teleport version
TELEPORT_DEMO_VERSION = "teleport-v3.1.1" 

# Specify a custom Vagrant box for the demo
DEMO_BOX_NAME = ENV['DEMO_BOX_NAME'] || "ubuntu/xenial64"

# Vagrantfile API/syntax version.
# NB: Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = DEMO_BOX_NAME
  config.vm.synced_folder ".", "/mnt"
  config.vm.provision "shell",
                          inline: $initiatescript,
                          env: {'TELEPORTL_DEMO_VERSION' => TELEPORT_DEMO_VERSION}

  config.vm.define "n1" do |n1|
      n1.vm.hostname = "n1"
      n1.vm.network "private_network", ip: "172.20.20.20"
      n1.vm.provision "shell",
                          inline: $teleportserver
  end
  config.vm.define "n2" do |n2|
      n2.vm.hostname = "n2"
      n2.vm.network "private_network", ip: "172.20.20.21"
      n2.vm.provision "shell",
                          inline: $teleportclient
  end
  config.vm.define "n3" do |n3|
      n3.vm.hostname = "n3"
      n3.vm.network "private_network", ip: "172.20.20.22"
      n3.vm.provision "shell",
                          inline: $teleportclient
  end
end
