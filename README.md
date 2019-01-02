# teleport-vagrant
teleport run using vagrant

# Howto
$ cd teleport-vagrant

$ wget -c https://get.gravitational.com/teleport-v3.1.1-linux-amd64-bin.tar.gz

$ vagrant up

$ vagrant ssh n1

$ sudo tctl nodes add --ttl=5m --roles=node

run the output on new node to join the cluster

$ sudo tctl users add $USER root

