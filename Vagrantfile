user = ENV['RH_SUBSCRIPTION_MANAGER_USER']
password = ENV['RH_SUBSCRIPTION_MANAGER_PW']
if !user or !password
  puts 'Required environment variables not found. Please set RH_SUBSCRIPTION_MANAGER_USER and RH_SUBSCRIPTION_MANAGER_PW'
  abort
end

register_script = %{
if ! subscription-manager status; then
    sudo subscription-manager remove --all
    sudo subscription-manager unregister
    sudo subscription-manager clean
    sudo yum clean all
    sudo rm -rf /var/cache/yum/*
    sudo subscription-manager register --username='#{user}' --password='#{password}' --auto-attach
fi
}

unregister_script = %{
if subscription-manager status; then
  sudo subscription-manager unregister
fi
}

Vagrant.configure(2) do |config|
    config.vm.box = "generic/rhel8"
   
    config.vm.network "forwarded_port", guest: 22, host:2222, id: "ssh", auto_correct: true

    config.vm.network "forwarded_port", guest: 80, host:8888, id: "apache", auto_correct: true

    config.vbguest.auto_update = false

    config.vm.define "rhl8_satellite" do |rhl8_satellite|
      rhl8_satellite.vm.provision "ansible" do |ansible|
        ansible.inventory_path = "staging"
        ansible.verbose = 'vvv'
        ansible.playbook = "site.yml"
      end
    
      config.vm.provider "virtualbox" do |v|
        v.memory = 8192
        v.name = "rhl8_satellite"
        v.cpus = 2
      end

      config.vm.provision "shell", inline: register_script

    end
  end