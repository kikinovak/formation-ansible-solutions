# Variables

## Exercice

Placez-vous dans le répertoire du quatorzième atelier pratique :

```
$ cd ~/formation-ansible/atelier-14
```

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


- Écrivez un *playbook* `myvars1.yml` qui affiche respectivement votre voiture
  et votre moto préférée en utilisant le module `debug` et deux variables
  `mycar` et `mybike` définies en tant que `play vars`.

```
---  # myvars1.yml

- hosts: localhost
  gather_facts: false

  vars:
    mycar: VW
    mybike: BMW

  tasks:
    - debug:
        msg: "My car: {{mycar}}. My bike: {{mybike}}."

...
```

- En utilisant les `extra vars`, remplacez successivement l’une et l’autre
  marque – puis les deux à la fois – avant d’exécuter le *play*.

```
$ ansible-playbook myvars1.yml -e mycar=Toyota
$ ansible-playbook myvars1.yml -e mybike=Kawasaki
$ ansible-playbook myvars1.yml -e mycar=Toyota -e mybike=Kawasaki
```

- Écrivez un *playbook* `myvars2.yml` qui fait essentiellement la même chose
  que `myvars1.yml`, mais en utilisant une tâche avec `set_fact` pour définir
  les deux variables.

```
---  # myvars2.yml

- hosts: localhost
  gather_facts: false

  tasks:

    - name: Define variables
      set_fact:
        mycar: VW
        mybike: BMW

    - debug:
        msg: "My car: {{mycar}}. My bike: {{mybike}}."

...
```

- Là aussi, essayez de remplacer les deux variables en utilisant des `extra
  vars` avant l’exécution du *play*.

```
$ ansible-playbook myvars2.yml -e mycar=Peugeot
$ ansible-playbook myvars2.yml -e mybike=Honda
$ ansible-playbook myvars2.yml -e mycar=Peugeot -e mybike=Honda
```

- Écrivez un *playbook* `myvars3.yml` qui affiche le contenu des deux variables
  `mycar` et `mybike` mais sans les définir. Avant d’exécuter le *playbook*,
  définissez `VW` et `BMW` comme valeurs par défaut pour `mycar` et `mybike`
  pour tous les hôtes, en utilisant l’endroit approprié.

```
---  # myvars3.yml

- hosts: all
  gather_facts: false

  tasks:

    - debug:
        msg: "My car: {{mycar}}. My bike: {{mybike}}."

...
```

Créer un répertoire `group_vars` à la racine du projet :

```
$ mkdir -v ~/ansible/projets/ema/group_vars
mkdir: created directory '/home/vagrant/ansible/projets/ema/group_vars'
```

Éditer un fichier `group_vars/all.yml` :

```
---  # group_vars/all.yml

mycar: VW
mybike: BMW

...
```

- Effectuez le nécessaire pour remplacer `VW` et `BMW` par `Mercedes` et
  `Honda` sur l’hôte `target02`.

Créer un répertoire `host_vars` à la racine du projet :

```
$ mkdir -v ~/ansible/projets/ema/host_vars
mkdir: created directory '/home/vagrant/ansible/projets/ema/host_vars'
```

Éditer un fichier `host_vars/target02.yml` :

```
---  # host_vars/target02.yml

mycar: Mercedes
mybike: Honda

...
```

- Écrivez un *playbook* `display_user.yml` qui affiche un utilisateur et son
  mot de passe correspondant à l’aide des variables `user` et `password`. Ces
  deux variables devront être saisies de manière interactive pendant
  l’exécution du *playbook*. Les valeurs par défaut seront `microlinux` pour
  `user` et `yatahongaga` pour `password`. Le mot de passe ne devra pas
  s’afficher pendant la saisie.

```
---  # display_user.yml

- hosts: localhost
  gather_facts: false

  vars_prompt:

    - name: user
      prompt: Please select a value for user
      default: microlinux
      private: false

    - name: password
      prompt: And now for the password
      default: yatahongaga
      private: true

  tasks:

    - debug:
        msg: "User: {{user}}  Password: {{password}}"

...
```

