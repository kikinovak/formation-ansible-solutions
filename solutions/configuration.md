# Configuration de base

## Exercice

Placez-vous dans le répertoire du sixième atelier pratique :

```
$ cd ~/formation-ansible/atelier-06
```

Démarrez les VM :

```
$ vagrant up
```

Connectez-vous au Control Host :

```
$ vagrant ssh control
```

Éditez `/etc/hosts` de manière à ce que les *Target Hosts* soient joignables
par leur nom d’hôte simple :

```
# /etc/hosts
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control
192.168.56.20  target01
192.168.56.30  target02
192.168.56.40  target03
```

Configurez l’authentification par clé SSH avec les trois *Target Hosts* :

```
$ ssh-keyscan -t rsa target{01,02,03} >> .ssh/known_hosts
$ ssh-keygen
$ ssh-copy-id vagrant@target01
...
vagrant@target01's password: *******
Number of key(s) added: 1
$ ssh-copy-id vagrant@target02
...
vagrant@target02's password: *******
Number of key(s) added: 1
$ ssh-copy-id vagrant@target03
...
vagrant@target03's password: *******
Number of key(s) added: 1
```

> Le mot de passe sur chacune des cibles est `vagrant`.

Installez Ansible :

```
$ sudo apt update
$ sudo apt install -y ansible
```

Envoyez un premier `ping` Ansible sans configuration :

```
$ ansible all -i target01,target02,target03 -m ping
```

Créez un répertoire de projet `~/monprojet` :

```
$ mkdir -v ~/monprojet
mkdir: created directory '/home/vagrant/monprojet'
```

Créez un fichier vide `ansible.cfg` dans ce répertoire :

```
$ cd monprojet/
$ touch ansible.cfg
```

Vérifiez si ce fichier est bien pris en compte par Ansible :

```
$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```

Spécifiez un inventaire nommé `hosts` :

```
$ cat ansible.cfg
[defaults]
inventory = ./hosts
```

Activez la journalisation dans `~/journal/ansible.log` :

```
$ mkdir -v ~/journal
mkdir: created directory '/home/vagrant/journal'
$ cat ansible.cfg
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

Testez la journalisation :

```
$ ansible all -i target01,target02,target03 -m ping
$ cat ~/journal/ansible.log
```

Créez un groupe `[testlab]` avec vos trois *Target Hosts*.

```
$ cat hosts
[testlab]
target01
target02
target03
```

Définissez explicitement l’utilisateur `vagrant` pour la connexion à vos cibles :

```
$ cat hosts
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
```

Envoyez un `ping` Ansible vers le groupe de machines `[all]` :

```
$ ansible all -m ping
```

Définissez l’élévation des droits pour l’utilisateur `vagrant` sur les *Target
Hosts* :

```
$ cat hosts
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
ansible_become=yes
```

Affichez la première ligne du fichier `/etc/shadow` sur tous les *Target Hosts* :

```
$ ansible all -a "head -n 1 /etc/shadow"
```

Quittez le *Control Host* et supprimez toutes les VM de l’atelier.

```
$ exit
$ vagrant destroy -f
```

