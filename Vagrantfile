# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant 1.7+ automatically inserts a different
# insecure keypair for each new VM created. The easiest way
# to use the same keypair for all the workers is to disable
# this feature and rely on the legacy insecure key.
# config.ssh.insert_key = false
#
# Note:
# As of Vagrant 1.7.3, it is no longer necessary to disable
# the keypair creation when using the auto-generated inventory.

# Requires vagrant-host-shell

VAGRANTFILE_API_VERSION = "2"
MANAGERS = 1
WORKERS = 2
ANSIBLE_GROUPS = {
  "managers" => ["manager[1:#{MANAGERS}]"],
  "workers" => ["worker[1:#{WORKERS}]"],
  "elk" => ["manager[${MANAGERS}:${MANAGERS}]"],
  "influxdb" => ["manager[${WORKERS}:${WORKERS}]"],
  "all_groups:children" => [
    "managers",
    "workers",
    "elk",
    "influxdb"]
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "centos/7"

  config.vm.provider 'virtualbox' do |v|
    v.linked_clone = true if Vagrant::VERSION =~ /^1.8/
  end

  config.ssh.insert_key = false

  (1..MANAGERS).each do |manager_id|
    config.vm.define "manager#{manager_id}" do |manager|
      manager.vm.hostname = "manager#{manager_id}"
      manager.vm.network "private_network", ip: "192.168.56.#{20+manager_id}"
      manager.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
      end
    end
  end

  (1..WORKERS).each do |worker_id|
    config.vm.define "worker#{worker_id}" do |worker|
      worker.vm.hostname = "worker#{worker_id}"
      worker.vm.network "private_network", ip: "192.168.56.#{30+worker_id}"
      worker.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
      end

      # Only execute once the Ansible provisioner,
      # when all the workers are up and ready.
      if worker_id == WORKERS

        # Install any ansible galaxy roles
        #worker.vm.provision "shell", type: "host_shell" do |sh|
        #  sh.inline =  "cd ansible && ansible-galaxy install -r requirements.yml -p roles"
        #end

        worker.vm.provision "bootstrap", type: "ansible" do |ansible|
            ansible.limit = "all"
            ansible.playbook = "ansible/bootstrap.yml"
            ansible.verbose = "vv"
            ansible.groups = ANSIBLE_GROUPS
        end

        worker.vm.provision "nomad", type: "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "ansible/nomad.yml"
          ansible.verbose = "vv"
          ansible.groups = ANSIBLE_GROUPS
        end
      end
    end
  end
end
