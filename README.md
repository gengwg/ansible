# Ansible

Ansible playbooks

To run Ansible on remote host the host need to support Python.
When using the ping module Ansible creates a little Python script that
is copied to the remote and then runs the python script on the remote
host. Network equipment typically doesn't support Python.

## Configuration

Only 1 configuration file is processed. First one wins in the below list. 


* `ANSIBLE_CONFIG` (an environment variable)
* `ansible.cfg` (in the current directory)
* `.ansible.cfg` (in the home directory)
* `/etc/ansible/ansible.cfg`

http://docs.ansible.com/ansible/intro_configuration.html

## Usage

* Run a bash command against some machines.

```
ansible -i hosts somemachines -m command -a 'lsb_release -a'

ansible -i hosts some-dgx1 -m shell -a "nvidia-docker --version && nvidia-docker run nvidia-smi"
```

* Providing the sudo Password

```
ansible -i hosts test -m ping --ask-become-pass
ansible-playbook -i hosts myplaybook.yml --ask-become-pass
```
## Playbook

Always quote template expression brackets.

```
ERROR! Syntax Error while loading YAML.
  found unacceptable key (unhashable type: 'AnsibleMapping')

The error appears to be in '/home/gengwg/ansible/copy_ssh_keys.yml': line 23, column 14, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

      user: {{ user }}
             ^ here
We could be wrong, but this one looks like it might be an issue with
missing quotes. Always quote template expression brackets when they
start a value. For instance:

    with_items:
      - {{ foo }}

Should be written as:

    with_items:
      - "{{ foo }}"
```

If always a couple hosts timeout here and there, increase the SSH timeout in `ansible.cfg`:

```
 # SSH timeout
-#timeout = 10
+timeout = 20
```
