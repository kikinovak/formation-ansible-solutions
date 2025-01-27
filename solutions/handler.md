# Handler

## Exercice

Les VM tournent toutes sous Rocky Linux. Démarrez-les :

```
$ vagrant up
```

Connectez-vous au *Control Host* :

```
$ vagrant ssh control
```

Rendez-vous dans le répertoire du projet :

```
$ cd ansible/projets/ema/
direnv: loading ~/ansible/projets/ema/.envrc
direnv: export +ANSIBLE_CONFIG
$ ls -l
total 8
-rw-r--r--. 1 vagrant vagrant  65 Sep 19 14:26 ansible.cfg
-rw-r--r--. 1 vagrant vagrant 128 Sep 19 14:26 inventory
drwxr-xr-x. 2 vagrant vagrant   6 Sep 19 14:26 playbooks
```

L’inventaire des machines définit un groupe `[redhat]` avec les trois *Target
Hosts* :

```
$ cat inventory
[redhat]
target01
target02
target03

[redhat:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=yes
```

Écrivez un *playbook* `chrony.yml` qui assure la synchronisation NTP de tous
vos *Target Hosts* :

- Installez le paquet `chrony`.

- Activez et démarrez le service `chronyd` correspondant.

- Jetez un œil sur le fichier de configuration `/etc/chrony.conf` fourni par
  défaut.

- Installez une configuration personnalisée (cf. ci-dessous).

- Prenez en compte cette nouvelle configuration.

- Vérifiez la syntaxe correcte de votre *playbook* `chrony.yml`.

- Vérifiez l’idempotence de votre *playbook*.

Voici la configuration à installer :

``` 
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

> Attention : le service `chronyd` n’accepte pas la directive `reloaded`.
> Autrement dit, vous devez passer par un `systemctl restart chronyd` pour
> prendre en charge la nouvelle configuration. Il faudra donc utiliser la
> directive `restarted` dans le module `service`.

```
---  # chrony.yml

- hosts: redhat

  tasks:

    - name: Install Chrony
      dnf:
        name: chrony

    - name: Start & enable Chrony
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Configure Chrony
      copy:
        dest: /etc/chrony.conf
        mode: 0644
        content: |
          # /etc/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart Chrony

  handlers:

    - name: Restart Chrony
      service:
        name: chronyd
        state: restarted

...
```

