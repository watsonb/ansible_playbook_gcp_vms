# ansible_playbook_gcp_vms

A series of Ansible playbooks for configuration management of GCP virtual
infrastructure

## Requirements

- Google Cloud Platform account

- libcloud with GCE support (0.17.0+)

```bash
pip install apache-libcloud==2.2.0
```

- pycrypto

```bash
pip install pycrypto
```

- certificates

I'm not exactly sure this is necessary.  This is one of a myriad of
troubleshooting steps I performed that resulted in something working.  In a
fresh python virtualenv with `ansible` installed via `pip`, the below removed
`certifi==2018.1.18`

```bash
pip uninstall -y certifi && pip install certifi==2015.04.28
```

- selinux

This is kind of a pain in the butt if, like me, you like to use and abuse
python virtual environments. see [this blog post](https://dmsimard.com/2016/01/08/selinux-python-virtualenv-chroot-and-ansible-dont-play-nice)

You have a couple of workarounds:

1. put this in your inventory (can sometimes mess up other plays)

```
localhost ansible_python_interpreter=/usr/bin/python
```

2. copy the `selinux` python module from your system site-packages into your
virtualenv's site-packages

```bash
sudo cp -R /usr/lib64/python2.7/site-packages/selinux <your_venv>/site-packages
sudo chown -R <appropriate user/group> <your_venv>/lib64/python2.7/site-packages/selinux
```

## Inventory Dependencies

 - See [inventories/README.md](./inventories/README.md) for more information

## Role Dependencies

 - See [requirements.yml](./requirements.yml) for a list of roles this playbook depends upon.
 - See [roles/README.md](./roles/README.md) for more information

## Example Playbooks

### Provision GCP Environment

If you wanted to provision an entire GCP environment "from nothing":

    ansible-playbook -i inventories/<your_inventory> provisionGcpEnvironment.yml
    
If you've protected your secrets (e.g. passwords) in an Ansible vault as part of
your inventory (good idea)

    ansible-playbook -i inventories/<your_inventory> provisionGcpEnvironment.yml --ask-vault-pass
    
### Ansible Play Tags

We have tagged most of the top-level plays in the [provisionGcpEnvironment.yml](./provisionGcpEnvironment.yml) playbook
such that you have influence over what gets done (or skipped) at runtime.
        
### Completely Delete GCP Environment

We have included a quick playbook that simply deletes the VMs that were created
via the `provisionGcpEnvironment.yml` playbook.  The rationale here is that sometimes
undoing things is difficult, and the easiest was to undo something is to redo it.

    ansible-playbook -i inventories/<your_inventory> deleteGcpEnvironment.yml --ask-vault-pass

## License

MIT

## Author Information

|Author|E-mail|
|---|---|
|Ben Watson|bwatson1979@gmail.com|