# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

HOSTNAME = 'devbox'
DOMAIN = 'vm'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "#{HOSTNAME}.#{DOMAIN}" do |app|
    app.vm.hostname = HOSTNAME
    app.vm.box = 'ubuntu/trusty64'

    # Override the default forwarded SSH port to ensure auto_correct is on.
    app.vm.network :forwarded_port, guest: 22, host: 2222, id: 'ssh', auto_correct: true
    app.vm.network :forwarded_port, guest: 3000, host: 3000, auto_correct: true

    # Adjust cores and memory,
    # courtesy of http://www.stefanwrobel.com/how-to-make-vagrant-performance-not-suck
    # Give this VM 1/4 system memory or 1GB, whichever is smaller,
    #  & access to all cpu cores on the host.
    app.vm.provider "virtualbox" do |v|
      host = RbConfig::CONFIG['host_os']
      if host =~ /darwin/
        cpus = `sysctl -n hw.ncpu`.to_i
        # sysctl returns Bytes and we need to convert to MB
        mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
      elsif host =~ /linux/
        cpus = `nproc`.to_i
        # meminfo shows KB and we need to convert to MB
        mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
      else # sorry Windows folks, I can't help you
        cpus = 2
        mem = 1024
      end
      mem = 1024 if mem > 1024
      v.customize ["modifyvm", :id, "--memory", mem]
      v.customize ["modifyvm", :id, "--cpus", cpus]
    end

    # Two-stage provisioning: first, set up the login user, "ubuntu".
    # Make sure to 'ssh-add' one of the SSH keys named in login_ssh_key_local_paths.
    app.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/canonicalize-ubuntu.yml"
      ansible.extra_vars = { login_ssh_key_local_paths: [ "~/.ssh/id_rsa.pub" ], initial_user: "vagrant" }
    end
    # Then, use the login user account to SSH in and set up everything
    # else.
    app.vm.provision "ansible" do |ansible|
      ansible.playbook = 'ansible/provision.yml'
      ansible.extra_vars = { upgrade_packages: "true" }
    end
  end
end
