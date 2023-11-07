# DOCKER-TRAINING

-----------------------------------------------------------------------------------------
# TP N°1/Lab-1 : docker-installation
-----------------------------------------------------------------------------------------
- Créez un dossier docker-installation
- Déplacez-vous dans ce dossier

- Initialisez vagrant afin de configurer la Vm à partir des informations suivantes :
    . Image de base => geerlingguy/centos7 (vagrant init geerlingguy/centos7),
    . CPU => 2,
    . RAM => 1 Go
- Variabilisez les paramètres indiquées ci-dessus

- Créer un fichier install-docker.sh pour contenir le script d'installation de docker
- Démarrer la VM (vagrant up)
- Connecter-vous en ssh (vagrant ssh nom_de_la_machine)
- Supprimez la VM à chaud après le TP (vagrant destroy -f)

-----------------------------------------------------------------------------------------
# Comandes utiles:
-----------------------------------------------------------------------------------------
- vagrant up : pour lancer vagrant
- vagrant ssh : pour se connecter en ssh à une VM
- vagrant halt : pour arreter une VM
- vagrant suspend : pour mettre en pause une VM
- vagrant provision : pour reconfigurer une VM
- vagrant destroy : pour supprimer une VM
