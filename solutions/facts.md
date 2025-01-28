# Facts et variables implicites

## Exercice

Placez-vous dans le répertoire du seizième atelier pratique :

```
$ cd ~/formation-ansible/atelier-16
```

Démarrez les VM :

```
$ vagrant up
```

Connectez-vous au Control Host :

```
$ vagrant ssh ansible
```

Rendez-vous dans le répertoire des playbooks :

```
$ cd ansible/projets/ema/playbooks/
direnv: loading ~/ansible/projets/ema/.envrc
direnv: export +ANSIBLE_CONFIG
```

Écrivez trois *playbooks* pour afficher des informations sur chacun des *Target
Hosts* :

- `pkg-info.yml` pour afficher le gestionnaire de paquets utilisé

---  # pkg-info.yml

```
- hosts: all

  tasks:

    - name: Display package manager
      debug:
        msg: >-
          Host [{{inventory_hostname}}] is using the
          {{ansible_pkg_mgr}} package manager.

...
```

- `python-info.yml` pour afficher la version de Python installée

---  # python-info.yml

```
- hosts: all

  tasks:

    - name: Display Python information
      debug:
        msg: >-
          Host [{{inventory_hostname}}] is running Python
          {{ansible_python_version}}.

...
```

- `dns-info.yml` pour afficher le(s) serveur(s) DNS utilisé(s)

```
---  # dns-info.yml

- hosts: all

  tasks:

    - name: Display DNS servers
      debug:
        msg: >-
          Host [{{inventory_hostname}}] is using these DNS servers:
          {{ansible_dns.nameservers}}

...
```
