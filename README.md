# ansible
ansible playbooks

* Run a bash command against some machines.

```
ansible -i hosts somemachines -m command -a 'lsb_release -a'
```

