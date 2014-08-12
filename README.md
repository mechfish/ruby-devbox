# Ruby-Devbox: A basic Ruby development VM.

- Compatible with both local Vagrant/Virtualbox environments and Digital Ocean cloud droplets.
- Ubuntu 14.04 "Trusty"
- Configured via a deliberately simplified Ansible configuration.
- Includes Ruby 2.1 installed from [Brightbox packages](http://brightbox.com/docs/ruby/ubuntu/).
- Recent stable version of Rails, plus various bonus gems.
- SQLite only.
- Configures the Linux firewall via UFW.
- Includes bonus CLI packages like `git`, `tmux`, `screen`, etc.

## Run locally with Vagrant

Out of the box, you may need an SSH key on your local machine at
`~/.ssh/id_rsa.pub` to make this work. You can generate one using
`ssh-keygen`. I may remove this requirement later, but since you need
an SSH key for Digital Ocean anyway...

- Install [Virtualbox](https://www.virtualbox.org).
- Install [Vagrant](http://www.vagrantup.com).
- Install [Ansible](http://docs.ansible.com/intro_installation.html#installing-the-control-machine). e.g. on the Mac with Homebrew: `brew update`, `brew install ansible`.

- `git clone https://github.com/mechfish/ruby-devbox.git`
- `cd ruby-devbox`
- `vagrant up`
- `vagrant ssh` to connect to the box.

## Run remotely with Digital Ocean

- Install Ansible, as above.
- Create an account at [Digital Ocean](https://www.digitalocean.com).
- Add your SSH key to the Digital Ocean account before you launch a droplet.
- Launch a droplet. Make sure you have it use your SSH key.
- When your droplet comes up take note of its IP address.
- Connect to the IP address for practice, and to teach it to your `known_hosts` file:

    ```
    ssh root@[IP address]
    ```

- `git clone https://github.com/mechfish/ruby-devbox.git`

- `cd ruby-devbox`

- Edit the `ansible/inventory` file and paste your droplet's IP
  address under the `[remote]` section.

- `rake ocean:init`
- `rake ocean:provision`

## Other fun activities

This project is basically just a couple of Ansible playbooks and a
very stupid inventory file. The Ansible playbooks are deliberately
flat, not importing lots of roles or variables -- so that you can read
'em in one glance and tinker to your heart's content.

- Edit the lists of Ubuntu packages.

- Edit the list of gems to install.

- Try to figure out why this is so slow even after the first
  run.
  (*Hint: installing packages with 'state=latest' forces Ansible
  to contact the repositories on every run. Repositories tend to be
  incredibly slow. Consider replacing 'state=latest' with
  'state=installed'. Of course, now re-running your Ansible config
  won't auto-update your packages, but that's not always a bad thing...*)

- Install a database. For hints, you might want to check out some
open-source Ansible roles like [DavidWittman.redis](https://github.com/DavidWittman/ansible-redis),
[nickjj.postgres](https://github.com/nickjj/ansible-postgres),
or [Ansibles.mysql](https://github.com/Ansibles/mysql).
