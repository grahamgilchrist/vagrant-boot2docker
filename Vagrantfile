VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.6.3"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "boot2docker"

  config.vm.box = "yungsang/boot2docker"
  config.vm.box_check_update = false
  config.vm.network "private_network", ip: "192.168.33.10"
  
  # Pass common server ports back to host machine
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.network "forwarded_port", guest: 9000, host: 9000

  # Mount current dir under same path in VM
  config.vm.synced_folder "/Users", "/Users", type: "nfs", mount_options: ["nolock", "vers=3", "udp"]

  config.vm.provider :virtualbox do |vb, override|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # Fix busybox/udhcpc issue
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      if ! grep -qs ^nameserver /etc/resolv.conf; then
        sudo /sbin/udhcpc
      fi
      cat /etc/resolv.conf
    EOT
  end

  # Adjust datetime after suspend and resume
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      sudo /usr/local/bin/ntpclient -s -h pool.ntp.org
      date
    EOT
  end

  # print out the machine IP
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      ifconfig | grep -A 1 eth1 | grep "inet addr:"
    EOT
  end

end
