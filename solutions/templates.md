# Jinja & Templates

## Exercice

Dans notre précédent exercice, nous avons mis en place la synchronisation NTP
avec Chrony sur nos *Target Hosts*, en installant le fichier de configuration
ci-dessous sur chacune des quatre cibles :

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

Écrivez un *playbook* `chrony.yml` qui installe un fichier de configuration
personnalisé sur vos cibles. La première ligne de commentaire devra indiquer le
chemin complet vers le fichier :

- Dans certains cas ce sera `/etc/chrony/chrony.conf`.

- Dans d’autres cas ce sera simplement `/etc/chrony.conf`.

Voici le *playbook* :

```
---  # chrony.yml

- hosts: all

  tasks:

    - name: Parameters for Debian/Ubuntu
      set_fact:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc/chrony
      when: ansible_os_family == "Debian"

    - name: Parameters for Rocky and SUSE
      set_fact:
        chrony_package: chrony
        chrony_service: chronyd
        chrony_confdir: /etc
      when: ansible_distribution in ["Rocky", "openSUSE Leap"]

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony
      package:
        name: "{{chrony_package}}"

    - name: Start Chrony service & enable it on boot
      service:
        name: "{{chrony_service}}"
        state: started
        enabled: true

    - name: Configure Chrony
      template:
        src: chrony.conf.j2
        dest: "{{chrony_confdir}}/chrony.conf"
      notify: Restart Chrony service

  handlers:

    - name: Restart Chrony service
      service:
        name: "{{chrony_service}}"
        state: restarted

...
```

Et voici le *template* `templates/chrony.conf.j2` qui va avec :

```
# {{chrony_confdir}}/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

