# Cibles hétérogènes

## Exercice

Placez-vous dans le répertoire du dix-septième atelier pratique :

```
$ cd ~/formation-ansible/atelier-17
```

Démarrez les VM :

```
$ vagrant up
```

Connectez-vous au *Control Host* :

```
$ vagrant ssh ansible
```

Rendez-vous dans le répertoire des playbooks :

```
$ cd ansible/projets/ema/playbooks/
direnv: loading ~/ansible/projets/ema/.envrc
direnv: export +ANSIBLE_CONFIG
```

Écrivez successivement deux *playbooks* pour mettre en place la synchronisation
NTP avec Chrony sur vos quatre *Target Hosts* sous Debian, Rocky Linux, SUSE
Linux et Ubuntu. Il vous faudra identifier le nom du paquet, le service
correspondant et le chemin vers le fichier de configuration par défaut pour
chacune des distributions.

Voici la configuration qu’il faudra installer sur chacune des quatre cibles :

```
# chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

- Le premier *playbook* `chrony-01.yml` utilisera les modules de gestion de
  paquets natifs `apt`, `dnf` et `zypper` et s’inspirera de la méthode "gros
  sabots" utilisée plus haut dans cet article.

```
---  # chrony-01.yml

- hosts: all

  tasks:

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Debian/Ubuntu
      apt:
        name: chrony
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Rocky Linux
      dnf:
        name: chrony
      when: ansible_distribution == "Rocky"

    - name: Install Chrony on SUSE Linux
      zypper:
        name: chrony
      when: ansible_distribution == "openSUSE Leap"

    - name: Start Chrony & enable it on boot
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Configure Chrony on Debian/Ubuntu
      copy:
        dest: /etc/chrony/chrony.conf
        mode: preserve
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_os_family == "Debian"
      notify: Restart Chrony

    - name: Configure Chrony on Rocky and SUSE
      copy:
        dest: /etc/chrony.conf
        mode: preserve
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_distribution in ["Rocky", "openSUSE Leap"]
      notify: Restart Chrony

  handlers:

    - name: Restart Chrony
      service:
        name: chronyd
        state: restarted

...
```

- Le deuxième playbook `chrony-02.yml` définira trois variables
  `chrony_package`, `chrony_service` et `chrony_confdir` et utilisera le module
  de gestion de paquets générique `package`.

```
---  # chrony2.yml

- hosts: all

  vars:
    chrony:
      Debian:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc/chrony
      Ubuntu:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc/chrony
      Rocky:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc
      openSUSE Leap:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc

  tasks:

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony
      package:
        name: "{{chrony[ansible_distribution].chrony_package}}"

    - name: Start Chrony & enable it on boot
      service:
        name: "{{chrony[ansible_distribution].chrony_service}}"
        state: started
        enabled: true

    - name: Configure Chrony
      copy:
        dest: "{{chrony[ansible_distribution].chrony_confdir}}/chrony.conf"
        mode: preserve
        content: |
          # chrony.conf
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

