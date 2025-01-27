# Variables enregistrées

## Exercice

Placez-vous dans le répertoire du quinzième atelier pratique :

```
$ cd ~/formation-ansible/atelier-15
```

Démarrez les VM :

```
$ vagrant up
```

Connectez-vous au *Control Host* :

```
$ vagrant ssh ansible
```

Rendez-vous dans le répertoire des *playbooks* :

```
$ cd ansible/projets/ema/playbooks/
direnv: loading ~/ansible/projets/ema/.envrc
direnv: export +ANSIBLE_CONFIG
```

- Écrivez un *playbook* `kernel.yml` qui affiche les infos détaillées du noyau
  sur tous vos *Target Hosts*. Utilisez la commande `uname -a` et le module
  `debug` avec le paramètre `msg`.

```
---  # kernel.yml

- hosts: all
  gather_facts: false

  tasks:

    - name: Get kernel parameters
      command: uname -a
      changed_when: false
      register: kernel_parameters

    - debug:
        msg: "{{kernel_parameters.stdout_lines}}"

...
```

- Essayez d’obtenir le même résultat en utilisant le paramètre `var` du module
  `debug`.

```
---  # kernel2.yml

- hosts: all
  gather_facts: false

  tasks:

    - name: Get kernel parameters
      command: uname -a
      changed_when: false
      register: kernel_parameters

    - debug:
        var: kernel_parameters.stdout_lines

...
```

- Écrivez un *playbook* `packages.yml` qui affiche le nombre total de paquets
  RPM installés sur les hôtes `rocky` et `suse` (`rpm -qa | wc -l`).

```
---  # packages.yml

- hosts: rocky, suse
  gather_facts: false

  tasks:

    - name: Count packages
      shell: rpm -qa | wc -l
      changed_when: false
      register: packages

    - debug:
        msg: "{{packages.stdout_lines}}"

...
```

