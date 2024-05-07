# lzap's Fedora dev setup

Ansible playbook for setting up my Fedora development box. Probably not very
useful to you.

This is only tested on Fedora both as an Ansible host and target system.

Warning: This playbook does some things which are not recommended for
production or real use like turning off SELinux or in-memory only journal.

## How to use this

On the Ansible host:

    dnf install ansible
    ansible-galaxy collection install fedora.linux_system_roles
    ansible-galaxy install linux-system-roles.tuned

Before executing the, make sure networking is correctly set up, custom routes
configured and IPv6 is disabled since networking is unmanaged via Ansible.
Modules do not use become/sudo, deploy a SSH key to the root account and permit
login via keys:

    mkdir -p /root/.ssh
    echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEhnn80ZywmjeBFFOGm+cm+5HUwm62qTVnjKlOdYFLHN lzap@box.home.lan" >> /root/.ssh/authorized_keys
    chmod 600 /root/.ssh/authorized_keys
    echo "PermitRootLogin prohibit-password" > /etc/ssh/sshd_config.d/prohibit-root-password
    systemctl restart sshd

Because journald will be configured to transient mode, old logs can be deleted:

    journalctl --rotate --vacuum-files=1

To execute the playbook:

    ANSIBLE_DISPLAY_SKIPPED_HOSTS=false ANSIBLE_DISPLAY_OK_HOSTS=false ansible-playbook -i root@dev.home.lan, dev.yaml

## Private assets

This public Ansible playbook does not contain any sensitive data, here are few commands which I need to run manually. SSH keys:

    scp $HOME/.ssh/{id_ed25519,id_rsa,fedorapeople_rsa}{,.pub} lzap@dev.home.lan:.ssh

## LICENSE

Public domain.
