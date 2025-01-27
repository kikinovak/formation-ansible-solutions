# Installation

## Exercice 1

Démarrez la VM `ubuntu` depuis le répertoire `atelier-01` :

```
$ vagrant up ubuntu
```

Connectez-vous à cette VM :

```
$ vagrant ssh ubuntu
```

Rafraîchissez les informations sur les paquets :

```
$ sudo apt update
```

Recherchez le paquet `ansible` avec les options qui vont bien :

```
$ apt-cache search --names-only ansible
```

Installez le paquet officiel fourni par la distribution :

```
$ sudo apt install ansible
```

Vérifiez si l’installation s’est bien déroulée :

```
$ ansible --version
ansible 2.10.8
```

Notez la version d’Ansible :

- 2.10.8

Déconnectez-vous et supprimez la VM :

```
$ exit
$ vagrant destroy -f ubuntu
```

## Exercice 2

Répétez l’exercice précédent en configurant un dépôt PPA (*Personal Package
Archive*) pour Ansible :

```
$ vagrant up ubuntu
$ vagrant ssh ubuntu
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt update
$ sudo apt install ansible
```

Notez la version fournie par ce dépôt tiers et comparez avec la version
officielle de l’exercice précédent :

```
$ ansible --version
ansible [core 2.17.7]
$ exit
$ vagrant destroy -f ubuntu
```

- 2.17.7 > 2.10.8

# Exercice 3

Lancez une VM Rocky Linux et installez Ansible en utilisant PIP et Virtualenv :

```
$ vagrant up rocky
$ vagrant ssh rocky
$ python3 -m venv ~/.venv/ansible
$ source ~/.venv/ansible/bin/activate
(ansible) $ pip install --upgrade pip
(ansible) $ pip install ansible
(ansible) $ ansible --version
ansible [core 2.15.13]
(ansible) $ deactivate
$ exit
$ vagrant destroy -f rocky
```

