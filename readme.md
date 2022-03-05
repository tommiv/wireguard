# Why

Definetely not to fool RKN, we all know it's not possible.

# Requirements

## Provisioning machine

You need anything that can run Ansible – Mac OS, any Linux, Windows + WSL2, and Ansible itself. Consult with [Ansible manual](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) for installation routine.

## Remote VM

You need a VPS from your favourite provider. I'll use Hetzner.

Some tips: 
- I suggest to select the closest datacenter to your location, in my case it's Finland, Helsinki
- Don't forget to add your SSH key during the creation process, just paste the key in the web interface
- Select Ubuntu 20 as VM OS
- Either configure your VM to allow root SSH connection (not so secure but generally ok for our purposes; default option in Hetzner, but not a thing in Digital Ocean) or create a new user and allow him to login with SSH and do `sudo` without a password (more secure but requires additional steps, see [here](https://thucnc.medium.com/how-to-create-a-sudo-user-on-ubuntu-and-allow-ssh-login-20e28065d9ff))

## Ansible config

1. Create `_inventory/inventory.yaml`. Put as many servers as you need in form of:

```yaml
wireguard:
  hosts:
    helsinki:
      ansible_host: 127.0.0.1
    moscow:
      ansible_host: 127.0.0.1
```

Don't forget to replace names and IPs with your real ones

2. Create `_inventory/ssh.yaml` in form of:

```yaml
all:
  vars:
    ansible_user: root
    ansible_ssh_private_key_file: "~/.ssh/id_rsa"
```

Don't forget to replace `root` with your SSH user if needed. Don't forget to replace the path to your SSH private key if needed. If you use your default SSH keypair and decided to allow root to login, there's nothing you need to edit here, just leave the created file as is.

3. Run `ansible-playbook ping.yaml`. You should see something like this in the last line:

```
wireguard                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

If you see an error... fix it.

## Wireguard setup

If you want some control over the installation, check the `_options.yaml` file which establishes some sensible defaults. You can change anything you want if you know what you do. To run setup it, do

```
ansible-playbook setup.yaml
```

and wait for a while.

## Setup a client

1. Register the peer and obtain the config
Run `ansible-playbook register-peer.yaml` and put an id when prompted.

2. Find your config in `./clients/${peer_id}.conf` and `./clients/${peer_id}.png` in the root dir of the project where you ran the playbook.

3. Download && install an app from the [official site](https://www.wireguard.com/install/). If you're on desktop, use `.conf` file to create a tunnel from it. If you on mobile, scan the QR-code from `.png`.

4. Optional. Google "my ip" or run `curl ifconfig.me` to ensure the Internet now thinks you're in Helsinki

# Credits

Based on [this article](https://linuxize.com/post/how-to-set-up-wireguard-vpn-on-ubuntu-20-04/)
