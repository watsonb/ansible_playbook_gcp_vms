# ansible_playbook_gcp_vms

A series of Ansible playbooks for configuration management of GCP virtual
infrastructure

## Requirements

- Google Cloud Platform account

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