# Inventories

Like roles, I feel inventories should be under source control, but ideally as
their own Git repos such that they can be _installed into_ the playbooks, but
not source controlled _with_ the playbooks.  This should facilitate a good
playbook becoming relatively stable and re-usable, where one merely "installs"
their own inventory to influence playbook execution.  The inventories will 
contain "localization information" (e.g. site/environment-specific 
configuration) and potentially sensitive information (e.g. passwords) 
protected in an Ansible vault.

# The .gitignore

The [.gitignore](../.gitignore) file for this playbook Git repository is
configured to ignore every file in this directory (inventories) except for
this README.md and the .gitkeep file to preserve the directory.  This facilitates
one's ability to "_install_" and "_uninstall_" inventories into this playbook
without making the playbook "_dirty_" from a source control point of view.

That is to say, a playbook author can now focus on playbook development and
use inventories without worrying about accidentally committing an inventory
to the playbook repository.

# Installing an Inventory

It is assumed that the inventory you want to install is itself under source
control, and I'll assume it is under Git.  We'll also assume you are at the
root folder of this playbook repo.

1. `cd inventories`
1. `mkdir <dir_name>`
1. `cd <dir_name>`
1. `git archive --remote <path_or_url_to_repo> <tag_or_branch_or_version> | tar xf -`

For example, assume Ben has an inventory on Bitbucket and wishes to install
the "develop" branch (version) of this inventory for use with these playbooks:

1. `cd inventories`
1. `mkdir ben`
1. `cd ben`
1. `git archive --remote ssh://git@bitbucket.di2e.net:7999/ispanauto/grdn_npes_ben.git develop | tar xf -`

You'll notice that if you perform a `git status` after performing this operation,
Git should tell you that your working directory (of the playbook itself) is
unchanged (unless of course you changed something else that is actually tracked)

# Running a Playbook Against an Inventory

Since Ansible honors passing the inventory to run against at runtime via the
command-line, you could actually have multiple inventories "installed"
simultaneously and run the same playbook against different inventories to
manage different sets of hosts.

Assume I have installed two inventories into subfolders named **foo** and 
**bar** and that they each have an actual inventory file named **foo_inv**
and **bar_inv** respectively.

Then to provision the "foo" NPES system:

    ansible-playbook -i inventories/foo/foo_inv provisionNPES.yml --ask-vault-pass 
    
And to provision the "bar" NPES system:

    ansible-playbook -i inventories/bar/bar_inv provisionNPES.yml --ask-vault-pass
    
**Note**: these two systems will have their own inventory files with a varied
number and names of hosts, and will likely define their own host and group
variables (e.g. protected sensitive information in a vault).

# Uninstalling an Inventory

Just delete it

1. `cd inventories`
1. `rm -rf <dir_name>`

Again, if you perform a `git status` after deleting an inventory, the current
working directory should remain "clean"