# Ansible
ansible playbooks

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
```

