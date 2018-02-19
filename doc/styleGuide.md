# MPAS Ansible Playbook Style Guide

This document captures guidance for authoring Ansible playbooks for MPAS. The
rationale for this guidance is to ensure that the playbooks are easier to
understand and maintain.

## General
The following are some general conventions covering YAML, Jinja, Ansible, etc.

### YAML
 - YAML files end with the **.yml** file extension (e.g. myPlay.yml)
 - YAML files should use 2 space indents
 - The first line in a YAML file is 3 dashes (e.g. ---)
 - YAML filenames should use camelCase to for word separation.
 
    Examples:
    installCertificates.yml
    big_bang.yml
    dumpAllVariables.yml

 - Use YAML **dictionary** style notation rather than inline notation unless inline notation is absolutely necessary
 
    Example: Dictionary Notation (Do this)
    ```yaml
    - name: FETCH | Fetch all signed certs from repo01
      fetch:
        src: "{{ certs_signed_dir }}/{{ item }}"
        dest: "{{ certs_base_dir }}/"
        flat: yes
      with_items: "{{ all_certs_files.stdout_lines }}"
    ```
      
    Example: Inline Notation (not this)
    ```yaml
    - name: FETCH | Fetch all signed certs from repo01
      fetch: src="{{ certs_signed_dir }}/{{ item }}" dest="{{ certs_base_dir }}/" flat=yes
      with_items: "{{ all_certs_files.stdout_lines }}"
    ```
      
### Variables
 - Use Jinja2 double curly brace variable syntax (e.g. `{{ myVar }}`)
 - Leave 1 space between the variable name and braces
 
    Example:
    Do this `{{ myVar }}`
    Not this `{{myVar}}`

 - Prefix variables defined within a role by the name of the role
 
    Example:
    You are working on a role named `stig`.
    You declare a variable within the stig role to hold version information (e.g. `version`).
    The name of the variable should be: `stig_version`
    
 - Prefix **vaulted** variables with `vault_` and then use the variable directly or assign another local variable's value to the vaulted variable
 
    Example:
    
    You need a password in a playbook but don't want to store the password as 
    plain text in the playbook.  You create an Ansible vault named 'myVault.yml'.
    Inside the vault, rather than using a variable named myPassword, you should 
    name it 'vault_myPassword'. In a subsequent playbook that needs access to 
    the password, you can either reference the vault variable directly or assign 
    it's value to a local variable.
    
    ```yaml
    - name: PLAY | Direct Usage Example
      hosts: hostA
      vars_files:
        - myVault.yml
      tasks:
      
        - name: SHELL | echo the password
          shell: echo {{ vault_myPassword }}
          
    - name: PLAY | Local Variable Assignment Example
      hosts: hostA
      vars_files:
        - myVault.yml
      vars:
        - myPassword: "{{ vault_myPassword }}"
      tasks:
      
        - name: SHELL | echo the password
          shell: echo {{ myPassword }}
    ```
          
    Prefixing vaulted variable names provides clues on where to 
    find/manage/access the actual values and that you'll need to provide a vault
    password to run the playbook.
    
### Plays vs. Playbooks
A **playbook** is a YAML file that contains one or more plays.  Name the playbook
(i.e. the YAML filename) according to what the playbook does using camelCase guidance
as described in the YAML section.  Execution of a playbook usually *does* something,
so think of verbs/actions when naming these files.  

A **play** is a construct inside a YAML file that has a name, indicates which 
host(s)/group(s) the play will run against, and the tasks/roles that will be 
applied to the host(s)/group(s).  Give your play a name that is concise but
meaningful (what does this play do).  This name will be printed during execution.

### Plays
Plays should primarily be used to apply roles to inventory members.

```yaml 
- name: PLAY | Configure Web Servers
  hosts: [webservers]
  roles:
    - { role: common }
    - { role: webserver, var1: foo, var2: bar }
```
 
### Roles
An Ansible role is a collection of tasks that are applied to a host to realize
some IT capability.  IT capabilities are *things* like webservers, databases,
load balancers, etc.  Things are nouns, hence role names should be nouns.

 - Role names should be nouns
 - Role names should be lower case
 - Separate words in role names with an underscore
 - Internally defined variables within a role should be prefixed by the role's name
 - Internally defined variables that should **not** be overwritten by plays should be defined in the role's *vars* folder
 - Internally defined variables that **should/may** be overwritten by plays should be defined in the role's *defaults* folder
 - The role's internal folder/file structure should match that of the included *role_template*
 - Leverage the role's README.md to accurately describe the role, it's variables, it's dependencies, etc. 
 - Leverage the role's meta/main.yml to accurately describe author, organization, Ansible/OS compatibility, and other role dependencies
