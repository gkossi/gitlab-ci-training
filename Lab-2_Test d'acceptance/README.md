# Infos sur le réalisateur du projet
Session       : Bootcamp DevOps N°15 de Eazytraining

Nom & Prénoms : GBENOU Kossi

LinkedIn      : https://www.linkedin.com/in/gkossi/

# LAB 2 : TEST D'ACCEPTANCE
- Modifiez le pipeline afin de rajouter un job qui lance l'image buildée et la teste à l'aide de curl
- Le curl doit tester si le site contient bien « Hello world ! »
- Vous aurez un soucis car l'image n'existera pas au moment où vous allez lancer le conteneur (la variable $PORT pourra etre mise à 80)
- Vous allez utiliser la notion d'artefact pour permettre à l'image d'etre vue par le job
- Indice : vous devez utiliser comme « path » le répertoire où docker stocke les images suivants (vous pouvez utiliser la notion d'import et export d'image docker)
- Si le test avec curl passe, alors vous etes pret pour la prochaine étape

# Les besoins
Pour mon travail j'aurai besoin de :

1) Créer et enregistrer mon propre runner privé à partir d'un conteneur Docker qui sera chargé d'exécuter le pipeline

Utiliser Virtualbox comme hyperviseur pour la création de machines virtuelles
Utiliser Vagrant comme provisionneur d'infrastructure pour gérer la VM
Installer Docker et Docker-compose sur la VM
Construire un conteneur pour chaque module (Backend & Fronted)
Faire interagir les conteneurs entre eux
Fournir un registre privé pour stocker les images

# Mon plan de travail


# Les fichiers et leurs roles chacun


# REALISATIONS ETAPE PAR ETAPE
Pour réaliser ce projet de A à Z, je vais suivre les étapes suivantes :

## Etape N°1: Création et enregistrement du runner privé à partir d'un conteneur Docker

*NB : On suppose que l'engine docker est déjà installé et est fonctionnelle

- Documentation pour la création et l'enregistrement du runner privé : https://docs.gitlab.com/runner/register

- On va créer un répertoire qui sera utilisé en sytem volume mount (bind mount) dans le conteneur docker qui servira de runner privé.
```bash
sudo mkdir -p /data/gitlab/runner
```
> ![1-Création du répertoire runner] ![](images/repertoire_data-gitlab-runner.png)

- On va créer le conteneur avec la méthode du "local system volume mounts" :
```bash
docker run -d --name gitlab-runner --restart always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /data/gitlab/runner:/etc/gitlab-runner \
gitlab/gitlab-runner:latest
```
> ![2-Création du runner] ![](images/runner.png)

- Une fois notre runner créé, il faut l'enregistrer pour qu'il soit visible sur gitlab.com pour etre utilisé.

Pour cela, je vais aller copier le token sur gitlab.com

> ![3-Copie du Token sur gitlab.com] ![](images/copie-token.jpg)

```bash
export TOKEN=mon-token-copié
```
> ![4-Enregistrement du runner] ![](images/enregistrement-token.png)

- Enregistrer le runner avec le jeton d'enregistrement (cette structure de commande s'applique seulement aux registre encours d'exécution dans un conteneur Docker) :
```bash
docker run --rm -v /data/gitlab/runner/:/etc/gitlab-runner gitlab/gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image docker:dind \
  --url "https://gitlab.com/" \
  --registration-token $TOKEN \
  --description "docker-runner-ggs" \
  --tag-list "docker-ggs" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected" \
  --docker-privileged \
  --docker-volumes '/var/run/docker.sock:/var/run/docker.sock'
```
> ![5-Enregistrement du runner] ![](images/enregistrement-runner.png)

> ![6-Vérification du runner sur gitlab.com] ![](images/runner-sur-gitlab.png)

NB : il faut s'assurer de désactiver le runner partagé sur gitlab.com


## Etape N°2: Configuration du fichier .gitlab-ci.yml pour la réalisation du BUILD de l'image

```bash
#Définition de l'environnement d'exécution de notre pipeline
image: docker:latest

#Création du service permettant d'utiliser du docker dans docker
services:
  - name: docker:dind

#Définition des différentes étapes
stages:
  - Build image

#JOB N°1 : Définition du job dans lequel sera exécuter l'étape du build de l'image
build:
  stage: Build image
  script:
    #Script pour builder l'image
    - docker build -t alpinehelloworld .
    #Script pour sauvegarder l'image en artefact
    - docker save alpinehelloworld > alpinehelloworld.tar
  #On va définir le fichier sauvegardé comme étant l'artefact
  artifacts:
    paths:
      - alpinehelloworld.tar
```
> ![7-Définition du JOB N°1 : BUILD DE L'IMAGE] ![](images/gitlab-ci_build.png)

> ![8-Lancement automatique du pipeline avec le runner privé] ![](images/lancement-pipeline.png)

> ![9-Vérification de sauvegarde de l'artifact] ![](images/verification-artifact.png)






