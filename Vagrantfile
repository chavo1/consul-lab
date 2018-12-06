Vagrant.configure(2) do |config|
    config.vm.box = "chavo1/xenial64base"

    1.upto(2) do |n|
      config.vm.define "consul0#{n}" do |box|
        box.vm.hostname = "consul0#{n}"
        box.vm.network "private_network", ip: "192.168.56.#{50+n}"

        box.vm.provision "shell", inline: <<-SCRIPT
            apt-get update
            apt-get install -y vim wget unzip curl jq
            cd /usr/local/bin

            wget https://releases.hashicorp.com/consul/1.4.0/consul_1.4.0_linux_amd64.zip
            unzip consul_1.4.0_linux_amd64.zip
        SCRIPT
      end
    end
  end
