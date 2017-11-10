This is an example of using vagrant-libvirt with ansible.

Why?

Because for having a working ansible setup mimicking production and at the same
time interacting with vagrant, one needs to:

- Take care of Vagrant ssh keys. Vagrant images ship with a known private ssh
  key, and vagrant deletes it and recreates the ssh on first boot. Ansible needs
  to be made aware of the ssh key so it can ssh and provision the machines.
- User an ansible deployment user: for production, it will be `deploy`, for
  vagrant, it will be `vagrant`.
- Setup a static IP network in libvirt so playbooks in ansible just need to
  contain the correct ip. This eliminates the need of vagrant plugins for
  recreating ansible playbooks.
- let Vagrant call the ansible provisioner when doing `vagrant up/provision`, yet
  allowing one to call ansible directly.


# Network layout #

- An isolated LAN network is simulated by providing an unmanaged switch, `switch_lan`.
- A static IP is created for ansible to provision the machines, `ansible_mgmt`,
  with NAT to the kvm host.
- Vagrant-libvirt plugin creates its own DHCP network with NAT to the kvm host for
  ssh-ing with the vagrant user, `vagrant-libvirt`.


# Deploying against Vagrant #

```bash
vagrant up
```

or if you want to call ansible outside of vagrant:

```bash
$ ansible-playbook -i inventories/vagrant -vv all.yml
```


# Deploying against production #

Ansible will use the `deploy` user to ssh (see roles/common/vars).

```bash
$ ansible-playbook -i inventories/production all.yml
```


## Setting up a new host in production ##

Yadda yadda:

```bash
$ adduser deploy
$ usermod -aG sudo deploy
```

``` bash
$ ssh-copy-id -f roles/common/files/deploy.pub <target host>
```
