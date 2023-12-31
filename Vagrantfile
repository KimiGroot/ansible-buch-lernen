$script = <<-'SCRIPT'

# direct:        Direktes root-Login moeglich
# sudo:          root via sudo
# sudo-nopasswd: root via sudo ohne Passworteingabe
# su:            root via su

ANSIBLE_USERPASS=ansible:ansible
   ROOT_USERPASS=root:ansible

useradd -m -s /bin/bash ansible
echo $ANSIBLE_USERPASS | chpasswd

if [ "$1" = "sudo" ]; then
   echo 'ansible ALL=(ALL) ALL' >/etc/sudoers.d/ansible
   sed -ri 's/^(PermitRootLogin).*/\1 no/' /etc/ssh/sshd_config
elif [ "$1" = "sudo-nopasswd" ]; then
   echo 'ansible ALL=(ALL) NOPASSWD: ALL' >/etc/sudoers.d/ansible
   sed -ri 's/^(PermitRootLogin).*/\1 no/' /etc/ssh/sshd_config
elif [ "$1" = "su" ]; then
   echo $ROOT_USERPASS | chpasswd
   sed -ri 's/^(PermitRootLogin).*/\1 no/' /etc/ssh/sshd_config
elif [ "$1" = "direct" ]; then
   echo $ROOT_USERPASS | chpasswd
   sed -ri 's/^(PermitRootLogin).*/\1 yes/' /etc/ssh/sshd_config
fi

systemctl restart sshd.service
SCRIPT


Vagrant.configure("2") do |config|

  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/debian-10"
    ansible.vm.hostname = "ansible"
    ansible.vm.network :private_network, ip: "192.168.56.99"
    ansible.vm.provider "virtualbox" do |p|
      p.memory = "1024"
      p.cpus = "2"
    end
    ansible.vm.provision :shell, inline: $script, :args => "sudo-nopasswd"
  end

  config.vm.define "debian" do |debian|
    debian.vm.box = "bento/debian-10"
    debian.vm.hostname = "debian"
    debian.vm.network :private_network, ip: "192.168.56.10"
    debian.vm.provider "virtualbox" do |p|
      p.memory = "1024"
      p.cpus = "2"
    end
    debian.vm.provision :shell, inline: $script, :args => "sudo"
  end

  config.vm.define "rocky" do |rocky|
    rocky.vm.box = "bento/rockylinux-8"
    rocky.vm.hostname = "rocky"
    rocky.vm.network :private_network, ip: "192.168.56.20"
    rocky.vm.provider "virtualbox" do |p|
    end
    rocky.vm.provision :shell, inline: $script, :args => "sudu"
  end

  config.vm.define "suse" do |suse|
    suse.vm.box = "bento/opensuse-leap-15.1"
    suse.vm.hostname = "suse"
    suse.vm.network :private_network, ip: "192.168.56.30"
    suse.vm.provider "virtualbox" do |p|
      p.memory = "1024"
      p.cpus = "2"
    end
    suse.vm.provision :shell, inline: $script, :args => "direct"
  end

  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "bento/ubuntu-20.04"
    ubuntu.vm.hostname = "ubuntu"
    ubuntu.vm.network :private_network, ip: "192.168.56.40"
    ubuntu.vm.provider "virtualbox" do |p|
      p.memory = "1024"
      p.cpus = "2"
    end
    ubuntu.vm.provision :shell, inline: $script, :args => "sudo-nopasswd"
  end

end
