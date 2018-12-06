Vagrant.configure(2) do |config|
    config.vm.box = "chavo1/xenial64base"

    1.upto(2) do |n|
      config.vm.define "consul0#{n}" do |box|
        box.vm.hostname = "consul#{n}"
        box.vm.network "private_network", ip: "192.168.56.5#{n}"

        box.vm.provision "shell", inline: <<-SCRIPT
  apt-get update
  apt-get install -y vim wget unzip
  cd /vagrant

  wget https://releases.hashicorp.com/consul/1.4.0/consul_1.4.0_linux_amd64.zip
  unzip consul_1.4.0_linux_amd64.zip
  mkdir ~/bin
  mv consul ~/bin
  echo 'export PATH=$PATH:~/bin' >> ~/.bashrc
  apt install -y curl jq
  source ~/.bashrc

        SCRIPT
      end
    end
  end