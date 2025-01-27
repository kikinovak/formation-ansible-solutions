# Authentification

## Exercice

Placez-vous dans le répertoire du troisième atelier pratique :

```
$ cd ~/formation-ansible/atelier-03
```

Démarrez les VM :

```
$ vagrant up
```

Connectez-vous au Control Host :

```
$ vagrant ssh control
```

Ansible est déjà installé sur cette machine :

```
$ type ansible
ansible is /usr/bin/ansible
```

Éditez le fichier `/etc/hosts` pour pouvoir travailler avec des noms d'hôtes :

```
$ sudo vim /etc/hosts
```

> Alternativement, vous pourrez utiliser `nano`.

Le fichier ressemblera à ceci :

```
# /etc/hosts
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control
192.168.56.20  target01
192.168.56.30  target02
192.168.56.40  target03
```

Collectez les clés publiques des *Target Hosts* :

```
$ ssh-keyscan -t rsa target{01,02,03} >> .ssh/known_hosts
# target02:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target01:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target03:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
```

Générez une paire de clés SSH :

```
$ ssh-keygen
```

Distribuez la clé publique sur les *Target Hosts* en fournissant à chaque fois
le mot de passe `vagrant` :

```
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

Envoyez un `ping` Ansible :

```
$ ansible all -i target01,target02,target03 -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Quittez la VM et faites le ménage :

```
$ exit
$ vagrant destroy -f
```

