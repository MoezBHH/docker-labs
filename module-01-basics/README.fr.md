# Lab 01 - Docker : Premiers conteneurs Alpine Linux

**Source :** [Play with Docker Training](https://training.play-with-docker.com/ops-s1-hello/)

<!-- Language Switcher -->
<p align="right">
  <a href="./README.md">English 🇬🇧</a> | <strong>Français</strong>
</p>

Dans cet atelier, vous allez explorer les bases du fonctionnement des conteneurs, ainsi que la manière dont le moteur Docker exécute et isole les conteneurs les uns des autres.

**Concepts abordés :**
* Moteur Docker (Docker Engine)
* Conteneurs et images
* Registres d'images et Docker Hub
* Isolation des conteneurs

---

## 1.0 Exécuter votre premier conteneur

Pour exécuter votre premier conteneur Docker, tapez :

```bash
docker container run hello-world
```

La sortie du conteneur `hello-world` vous en dit un peu plus sur ce qui vient de se passer. Essentiellement, le moteur Docker exécuté dans votre terminal a essayé de trouver une image nommée `hello-world`. Puisque vous venez de commencer, aucune image n'est stockée localement (`Unable to find image...`), le moteur Docker accède donc à son registre Docker par défaut, qui est **Docker Hub**, pour rechercher une image nommée « hello-world ». 

Il y trouve l'image, l'extrait, puis l'exécute dans un conteneur. La seule fonction de `hello-world` est d'afficher le texte que vous voyez dans votre terminal, ensuite le conteneur se ferme.

![Docker first container](https://training.play-with-docker.com/images/ops-basics-hello-world.svg)

### Questions :
1. Pensez-vous que cela revient à exécuter une machine virtuelle ?
2. Un conteneur est une abstraction :
   * a. matérielle
   * b. d'application
3. Est-il possible d'utiliser à la fois des machines virtuelles et des conteneurs dans un même environnement ? Comment ?

---

## 1.1 Images Docker

Dans la suite de cet atelier, vous allez exécuter un conteneur Alpine Linux. Alpine est une distribution Linux légère, elle est donc rapide à extraire et à exécuter, ce qui en fait un point de départ populaire pour de nombreuses autres images.

Pour commencer, exécutez ce qui suit dans votre terminal :

```bash
docker image pull alpine
```

La commande `pull` récupère l'image `alpine` du registre Docker et l'enregistre dans notre système. Dans ce cas, le registre est Docker Hub (vous pouvez modifier le registre).

Vous pouvez utiliser la commande `docker image` pour voir la liste de toutes les images de votre système. Exécutez dans votre terminal :

```bash
docker image ls
```

### Exécution du conteneur Docker

Exécutez maintenant un conteneur Docker basé sur cette image. Pour ce faire, vous allez utiliser la commande `docker container run` :

```bash
docker container run alpine ls -l
```

Lorsque vous appelez `run`, le client Docker trouve l'image (`alpine` dans ce cas), crée le conteneur puis exécute une commande dans ce conteneur. Lorsque vous avez exécuté `docker container run alpine ls -l`, vous avez fourni une commande (`ls -l`), donc Docker a exécuté cette commande dans le conteneur pour lequel vous avez vu la liste des répertoires. Une fois la commande `ls` terminée, le conteneur s'est arrêté.

Le fait que le conteneur se soit fermé après l'exécution de notre commande est important. 

![docker run details](https://training.play-with-docker.com/images/ops-basics-run-details.svg)


Testez les commandes suivantes et observez ce qu'il se passe :

```bash
docker container run alpine echo "hello from alpine"
```

```bash
docker container run alpine /bin/sh
```

```bash
docker container run -it alpine /bin/sh
```

Vous avez exécuté chacune de vos commandes ci-dessus dans une instance de conteneur distincte. Nous pouvons voir ces instances en utilisant la commande `docker container ls` qui liste les conteneurs en cours d'exécution :

```bash
docker container ls
```

Puisqu'aucun conteneur n'est en cours d'exécution, vous voyez une ligne vide. Essayons une variante plus utile :

```bash
docker container ls -a
```

*Que représente le résultat de cette commande ?*
Pour en savoir plus sur `run`, utilisez `docker container run --help` pour voir une liste de tous les indicateurs qu'il prend en charge.

---

## 1.2 Isolation des conteneurs

Dans les étapes ci-dessus, nous avons exécuté plusieurs commandes via des instances de conteneur à l'aide de `docker container run`. La commande `docker container ls -a` nous a montré qu'il y avait plusieurs conteneurs répertoriés.

**Pourquoi y a-t-il autant de conteneurs répertoriés s'ils proviennent tous de l'image alpine ?**

Il s'agit d'un concept de sécurité critique dans le monde des conteneurs Docker ! Même si chaque commande utilisait la même image `alpine`, chaque exécution était un **conteneur distinct et isolé**. Chaque conteneur possède un système de fichiers distinct et s'exécute dans un espace de noms différent ; par défaut, un conteneur n'a aucun moyen d'interagir avec d'autres conteneurs, même ceux de la même image. 

Essayons un autre exercice pour en savoir plus sur l'isolation :

```bash
docker container run -it alpine /bin/ash
```

`/bin/ash` est un autre type de shell disponible dans l'image alpine. Une fois le conteneur lancé et que vous êtes à l'invite de commande du conteneur, tapez les commandes suivantes :

```bash
echo "hello world" > hello.txt
ls
```

La première commande crée un fichier appelé `hello.txt` contenant les mots « hello world ». La deuxième commande vous donne une liste de répertoires et devrait afficher votre nouveau fichier. Tapez maintenant `exit` pour quitter ce conteneur.

Pour montrer comment fonctionne l'isolation, exécutez la commande suivante :

```bash
docker container run alpine ls
```

C'est la même commande `ls` que nous avons utilisée dans le shell interactif du conteneur, mais cette fois, avez-vous remarqué que votre fichier `hello.txt` est manquant ? **C'est l'isolation.** Votre commande s'est exécutée dans une nouvelle instance distincte. La 2ème instance n'a aucun moyen d'interagir avec la 1ère instance car le Docker Engine les maintient séparées.

À l'heure actuelle, la question évidente est : **« Comment puis-je revenir au conteneur contenant mon fichier hello.txt ? »**

Exécutez à nouveau :

```bash
docker container ls -a
```

Graphiquement, voici ce qui s'est passé sur notre Docker Engine :
![Docker Container Isolation](https://training.play-with-docker.com/images/ops-basics-isolation.svg)

Le conteneur dans lequel nous avons créé le fichier `hello.txt` est le même que celui dans lequel nous avons utilisé la commande shell `/bin/ash` (visible dans la colonne `COMMAND`). Le numéro d'ID du conteneur (ex: `3030c9c91e12`) identifie de manière unique cette instance.

Nous pouvons utiliser une commande pour indiquer à Docker de démarrer cette instance spécifique :

```bash
docker container start <container ID>
```
*(Conseil : au lieu d'utiliser l'ID complet, vous pouvez utiliser uniquement les premiers caractères, par exemple `3030`, à condition qu'ils soient uniques dans la liste).*

Utilisez à nouveau la commande `docker container ls` pour répertorier les conteneurs en cours d'exécution. L'instance est à nouveau active. Puisqu'elle attend une commande (via le shell ash), nous pouvons lui envoyer une commande en utilisant `exec` :

```bash
docker container exec <container ID> ls
```

Cette fois, la liste affiche bien notre fichier `hello.txt` car nous avons utilisé l'instance exacte dans laquelle nous avons créé ce fichier.

![docker container exec](https://training.play-with-docker.com/images/ops-basics-exec.svg)

---

## Terminologie de base

* **Démon Docker (Docker Daemon) :** Le service d'arrière-plan exécuté sur l'hôte qui gère la création, l'exécution et la distribution des conteneurs Docker.
* **Client Docker :** L'outil de ligne de commande (CLI) qui permet à l'utilisateur d'interagir avec le démon Docker.
* **Docker Hub :** Un registre public d'images Docker. Vous pouvez le considérer comme un répertoire de toutes les images Docker disponibles prêtes à être téléchargées et utilisées.