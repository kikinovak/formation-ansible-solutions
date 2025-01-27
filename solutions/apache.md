# Un serveur web simple

## Exercice

Placez-vous dans le répertoire du dixième atelier pratique :

$ cd ~/formation-ansible/atelier-10

Démarrez les VM :

$ vagrant up

Connectez-vous au Control Host :

$ vagrant ssh ansible

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

Écrivez trois playbooks :

- Un premier *playbook* `apache-debian.yml` qui installe Apache sur l’hôte
  `debian` avec une page personnalisée `Apache web server running on Debian
  Linux`.

```
---  # apache-debian.yml

- hosts: debian

  tasks:

    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Debian Linux</h1>
            </body>
          </html>

...
```

- Un deuxième *playbook* `apache-rocky.yml` qui installe Apache sur l’hôte
  `rocky` avec une page personnalisée `Apache web server running on Rocky
  Linux`.

```
---  # apache-rocky.yml

- hosts: rocky

  tasks:

    - name: Install Apache
      dnf:
        name: httpd

    - name: Start & enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Rocky Linux</h1>
            </body>
          </html>

...
```

- Un troisième *playbook* `apache-suse.yml` qui installe Apache sur l’hôte
  `suse` avec une page personnalisée `Apache web server running on SUSE Linux`.

```
---  # apache-suse.yml

- hosts: suse

  tasks:

    - name: Install Apache
      zypper:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on SUSE Linux</h1>
            </body>
          </html>

...
```

