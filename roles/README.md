# Roles

This is the folder where the roles required by the top playbooks depend
upon will be installed by default.  The intent here is to leverage the
`ansible-galaxy` tool to manage the roles in the folder.  Each role should
be fully self-contained and ideally its own Git repository that could be
searched on the Ansible Galaxy website and who's source could be obtained
by cloning (e.g. from GitHub, BitBucket, etc.) and installed via the
`ansible-galaxy` tool:

For example:

    ansible-galaxy install -r npesRequirements.yml -p roles
    
This command should install all of the identified required/dependent roles
in the `npesRequirements.yml` file into the roles folder.

As a playbook developer, you will need to maintain this list of requirements
and periodically install the roles.  You will then author playbooks which _apply_
the roles to the hosts in your inventory.  However, we do not want to include the
installed roles as source code within the Git commits to the playbook source
itself (i.e., these may not be _your_ roles, we don't want Git within Git, etc.).

Therefore, the top-level `.gitignore` is configured to ignore the entire contents
of this roles folder except for the `.gitkeep` file and this `README`.  If for some
reason you must develop a role specific to this set of playbooks that will not
be fully self-contained nor shared/managed as its own Git repo, then you will
have to update the top-level `.gitignore` to account for your special role.