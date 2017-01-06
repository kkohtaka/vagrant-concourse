# Copyright (c) 2016 Kazumasa Kohtaka. All rights reserved.
# This file is available under the MIT license.

$num_workers = 2

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  # concourse master
  config.vm.define "master" do |master|
    master.vm.network "private_network", ip: "192.168.1.10"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end

  # concourse worker
  (1..$num_workers).each do |index|
    config.vm.define "worker#{sprintf("%03d", index)}" do |worker|
      worker.vm.network "private_network", ip: "192.168.2.#{10+index}"
      worker.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
      end
    end
  end

  # postgres
  config.vm.define "db" do |db|
    db.vm.network "private_network", ip: "192.168.3.10"
    db.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end

  # provisioning
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.groups = {
      "master" => ["master"],
      "workers" => (1..$num_workers).map { |index| "worker#{sprintf("%03d", index)}" },
      "db" => ["db"]
    }
    ansible.extra_vars = {
      "ansible_python_interpreter" => "/usr/bin/python3"
    }
  end
end
