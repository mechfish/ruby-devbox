
def hosts_exist_in_group(group_name)
  `ansible #{group_name} -i ansible/inventory --list-hosts` !~ /No hosts matched/
end

def exit_if_no_hosts(group_name)
  unless hosts_exist_in_group(group_name)
    puts "No remote hosts found. Edit ansible/inventory and paste the hostname or IP in the '[remote]' section."
    exit 1
  end
end

namespace :ocean do
  desc "Initialize a Digital Ocean system"
  task :init do
    exit_if_no_hosts('remote')
    system 'ansible-playbook -i ansible/inventory --limit=remote --extra-vars="initial_user=root" ansible/canonicalize-ubuntu.yml'
  end

  desc "Provision a Digital Ocean system"
  task :provision do
    system 'ansible-playbook -i ansible/inventory --limit=remote ansible/provision.yml'
  end
end
