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

```
$ head ansible.cfg
# config file for ansible -- https://ansible.com/
# ===============================================

# nearly all parameters can be overridden in ansible-playbook
# or with command line flags. ansible will read ANSIBLE_CONFIG,
# ansible.cfg in the current working directory, .ansible.cfg in
# the home directory or /etc/ansible/ansible.cfg, whichever it
# finds first
```

## Usage

* Run a bash command against some machines.

```
ansible -i hosts somemachines -m command -a 'lsb_release -a'
ansible -i hosts some-dgx1 -m shell -a "nvidia-docker --version && nvidia-docker run nvidia-smi"
ansible -i hosts some-dgx1 -m shell -a "lspci | grep MT2890"
```

* Providing the sudo Password

```
ansible -i hosts test -m ping --ask-become-pass
ansible -i hosts test -m shell -a 'cat /etc/sudoers' -b --ask-become-pass
ansible -i hosts test -m shell -a "cat /etc/sudoers" -b -K  # shorter form equivalent to above
ansible-playbook -i hosts myplaybook.yml --ask-become-pass
```

* Check power supply units (PSU) status (worked on CentOS 7)

```
ansible -i hosts test -m shell -a 'ipmitool sdr type "Power Supply"' -b --ask-become-pass |  tee psu-status.txt

example output:

# Dell PowerEdge R740xd
dell-host | CHANGED | rc=0 >>
PS Redundancy    | 77h | ok  |  7.1 | Fully Redundant
Status           | 85h | ok  | 10.1 | Presence detected
Status           | 86h | ok  | 10.2 | Presence detected

# Nvidia DGX1
dgx-host | CHANGED | rc=0 >>
PSU Alert        | 34h | ns  | 208.1 | Event-Only
PSU Redundancy   | E8h | ok  | 21.1 | Non-Redundant: Insufficient Resources
PSU1 Status      | E0h | ok  | 10.1 | Presence detected, Power Supply AC lost
PSU2 Status      | E1h | ok  | 10.2 | Presence detected, Power Supply AC lost
PSU3 Status      | E2h | ok  | 10.3 | Presence detected
PSU4 Status      | E3h | ok  | 10.4 | Presence detected
```

## Examples

Check OFED versions:

```
$ ansible -i hosts mydgx -m shell -a 'apt list --installed | grep mlnx-ofed-kernel' > /tmp/ofed_version.txt
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
