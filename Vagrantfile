# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)", ip: "192.168.1.91"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    #   # Display thpe VirtualBox GUI when booting the machine
    #vb.gui = false
    #
    #   # Customize the amount of memory on the VM:
    vb.memory = "1024"
    vb.cpus = "1"
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    vb.customize ["modifyvm", :id, "--usb", "on"]
    vb.customize ["modifyvm", :id, "--usbehci", "on"]
    vb.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'ESP32', '--vendorid', '0x10c4', '--productid', '0xea60']
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
     hostname usbip-server

     sed -i '/PasswordAuthentication /d' /etc/ssh/sshd_config
     echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
     sed -i '/ChallengeResponseAuthentication /d' /etc/ssh/sshd_config
     echo "ChallengeResponseAuthentication no" >> /etc/ssh/sshd_config
     sudo systemctl restart sshd

     #echo 'vagrant' | openssl passwd -1 -stdin # $1$/QumeMpi$7DNrmqSaoOwOLF4QsDuw41
     echo 'vagrant:$1$/QumeMpi$7DNrmqSaoOwOLF4QsDuw41' | chpasswd -e

     apt -y install linux-tools-4.15.0-167-generic
     echo "deb-src http://archive.ubuntu.com/ubuntu bionic main" >> /etc/apt/sources.list
     echo "deb-src http://archive.ubuntu.com/ubuntu bionic-updates main"  >> /etc/apt/sources.list
     apt -y update
     apt -y build-dep linux linux-image-$(uname -r)
     apt -y source linux-image-unsigned-$(uname -r)
     cd linux-`uname -r|awk -F'-' '{print $1}'` #cd linux-4.15.0
     cd drivers/usb/usbip
     make -C /usr/src/linux-headers-`uname -r` M=`pwd` modules
     make -C /usr/src/linux-headers-`uname -r` M=`pwd` modules_install
     cd ../../../../
     depmod -a
     update-initramfs -u
     echo "modprobe usbip_core; modprobe usbip_host; usbipd -D;usbip bind -b 2-1" >> start.sh
     chmod a+x start.sh
     echo "pkill -9 usbipd; rmmod usbip_host usbip_core" >> cleanup.sh
     chmod a+x cleanup.sh
     echo "./cleanup.sh;./start.sh" >> restart.sh
     chmod a+x restart.sh
     ./start.sh
   SHELL
end
