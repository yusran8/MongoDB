# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "mongo_config_1" do |mongo_config_1|
    mongo_config_1.vm.hostname = "mongo-config-1"
    mongo_config_1.vm.box = "bento/ubuntu-18.04"
    mongo_config_1.vm.network "private_network", ip: "192.168.17.105"
    
    mongo_config_1.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-config-1"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_config_1.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

  config.vm.define "mongo_config_2" do |mongo_config_2|
    mongo_config_2.vm.hostname = "mongo-config-2"
    mongo_config_2.vm.box = "bento/ubuntu-18.04"
    mongo_config_2.vm.network "private_network", ip: "192.168.17.106"
    
    mongo_config_2.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-config-2"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_config_2.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

  config.vm.define "mongo_query_router" do |mongo_query_router|
    mongo_query_router.vm.hostname = "mongo-query-router"
    mongo_query_router.vm.box = "bento/ubuntu-18.04"
    mongo_query_router.vm.network "private_network", ip: "192.168.17.107"
    
    mongo_query_router.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-query-router"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_query_router.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

  config.vm.define "mongo_shard_1" do |mongo_shard_1|
    mongo_shard_1.vm.hostname = "mongo-shard-1"
    mongo_shard_1.vm.box = "bento/ubuntu-18.04"
    mongo_shard_1.vm.network "private_network", ip: "192.168.17.108"
        
    mongo_shard_1.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-shard-1"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_shard_1.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

  config.vm.define "mongo_shard_2" do |mongo_shard_2|
    mongo_shard_2.vm.hostname = "mongo-shard-2"
    mongo_shard_2.vm.box = "bento/ubuntu-18.04"
    mongo_shard_2.vm.network "private_network", ip: "192.168.17.109"
    
    mongo_shard_2.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-shard-2"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_shard_2.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

  config.vm.define "mongo_shard_3" do |mongo_shard_3|
    mongo_shard_3.vm.hostname = "mongo-shard-3"
    mongo_shard_3.vm.box = "bento/ubuntu-18.04"
    mongo_shard_3.vm.network "private_network", ip: "192.168.17.110"
    
    mongo_shard_3.vm.provider "virtualbox" do |vb|
      vb.name = "mongo-shard-3"
      vb.gui = false
      vb.memory = "512"
    end

    mongo_shard_3.vm.provision "shell", path: "provision/allhosts.sh", privileged: false
  end

end