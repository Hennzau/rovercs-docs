Comme ce qu'il est expliqué un peu dans la partie Hardware, pour développer le robot il faudra se connecter aux différentes cartes. Voici ce qu'il est possible de faire niveau software (les procédures d'installation viennent après) :

- Utiliser un simulateur du robot avec du code pour faire une pré vérification dans un environnement de test du fonctionnement des fonctionnalités développées
- Connecter le Rover (grâce à la NavQ+) à une interface de contrôle afin de le piloter depuis un ordinateur (possibilité de rajouter un joystick)
- Upload notre code ROS2 sur le robot (via MRCANHUB) afin de le faire fonctionner

# Installation clean d'un environnement

Il est actuellement *nécessaire* de posséder un ordinateur (de préférence portable) qui fonctionne avec Linux (de préférence un environnement simple comme Ubuntu) sous l'architecture x64 (donc pas de Macbook M1/M2/M3 désolé).
Il est également nécessaire de posséder un compte GitHub ! Et il faudra établir des autorisations SSH (mais pas de panique ça sera expliqué après).

## Docker

Comme tous les hardware sont différents et qu'on n'est pas à l'abris qu'un utilisateur ai déjà installé des trucs bizarre sur son ordinateur, pour ne pas compromettre l'intégrité de ses installations la compétition propose une image Docker pour la compétition.
C'est ce que nous allons utilisé car c'est toujours bien plus simple d'utilisé Docker. Voici un guide d'installation de Docker d'après leur site internet :

Copié collé les différents code dans un terminal (veillez à bien éxécuter le code entre chaque étape)

- Désinstallation de tous les packages qui existent en rapport avec une précédente installation de Docker :

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

- Récupérations des clées d'installation des différents packages :

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

- Installation des packages Docker :

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Vérification de l'installation : (vous devriez avoir un message de plusieurs lignes dont une contenant "Hello World")

```
sudo docker run hello-world
```

- Permissions d'utiliser docker pour un utilisateur :

```
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

- Lancement de docker au démarrage :

```
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

- Dernière vérification :

```
docker run hello-world
```

Voilà Docker est bien installé et vous êtes prêt à installé les softs.

## Image docker

Comme je l'ai précisé il existe une image du soft qui permet de tout faire simplement. Le code suivant permet de créer un répertoire, de cloner un repository qui contient l'image, et d'installer l'image. *Attention* l'installation de l'image peut prendre une dizaine de minute. Veillez à accepter tout ce qui peut vous être demandé lors de l'installation et à ne pas laisser votre ordinateur se mettre en veille

```
mkdir -p ~/cognipilot
cd ~/cognipilot
git clone -b <BRANCH> https://github.com/cognipilot/docker
cd  ~/cognipilot/docker
git submodule update --init --recursive
cd ~/cognipilot/docker/dream
./dream start
```

Maintenant que l'image docker est installée vous êtes prêt à développer. Pour tout ce qui va maintenant se passer il faudra pénétrer notre image docker, qui âgit comme un nouvel espace de travail, propre et conçu pour la compétition. Rien de ce que vous y ferez ne sera visible sur votre espace personnel d'origine et c'est ça qui est beau.

Pour pénétrer l'image docker utilisez toujours cette commande :

```
cd ~/cognipilot/docker/dream
./dream start
./dream exec
```

Pour quitter cet espace appuyez sur "CTRL+D" dans la console

## Autorisations de GitHub via SSH

Afin de faciliter la connexion avec github, on peut établir des autorisations via SSH. Pour ça il faut pénétrer l'image docker :

```
cd ~/cognipilot/docker/dream
./dream start
./dream exec
```


Ensuite il faut générer une clé SSH que l'on passera à GitHub  :

```
ssh-keygen -t ed25519 -C "votre_adresse_mail_github@example.com"
```

Cliquez ensuite toujours simplement sur votre touche "Entrée" jusqu'à ce que le logiciel ne vous demande plus rien. La clé sera alors créée, pour la récupérer faites :

```
cat ~/.ssh/id_ed25519.pub
```

Faites un copié collé du résultat en entier (sur certains terminaux dont celui de base pour Ubuntu il faut faire click droit copié, ou CTRL+MAJ+C après sélection)

- Ensuite dans GitHub, dans le coin supérieur droit de n'importe quelle page, cliquez sur votre photo de profil, puis sur Paramètres.

- Dans la section "Accès" de la barre latérale, cliquez sur Clés SSH et GPG.

- Cliquez sur Nouvelle clé SSH ou Ajouter une clé SSH.

- Dans le champ "Titre", ajoutez une étiquette descriptive pour la nouvelle clé. Par exemple, si vous utilisez un ordinateur portable personnel, vous pouvez appeler cette clé "Ordinateur portable personnel".

- Sélectionnez le type de clé authentification

- Dans le champ "Clé", collez votre clé publique.

- Cliquez sur Ajouter une clé SSH.

Si vous y êtes invité, confirmez l'accès à votre compte sur GitHub.

Maintenant vous avez le droit d'utiliser votre compte Github depuis votre ordinateur, sans authentification supplémentaire. ceci vous permet donc de pouvoir participer au développement du logiciel de ce rover !! Mais vous n'y êtes pas obligé, déjà c'est bien si on arrive à faire quelque chose de bien pour la NXPCup

## Compilation des différents softs

Normalement vous êtes toujours dans votre image docker mais si ce n'est pas le cas :

```
cd ~/cognipilot/docker/dream
./dream start
./dream exec
```

On dispose dans cette image d'un script pré conçue qui compile tous les logiciels dont on a besoin pour contrôler le robot, faire la simulation et upload notre code. Pour ça on a juste à éxécuter :

```
build_workspace
```

Il faut alors choisir `y to clone with ssh keys` puis `1 for b3rb`. Acceptez ensuite tout ce qu'on pourrait vous demander, notamment à la fin de la procédure vous êtes invité à éxécuter :

```
source ~/.bashrc
```

Il reste le logiciel de simulation à compiler pour ça, comme pour avant mais avant la commande

```
build_foxglove
```

Il faut alors choisir `y to clone with ssh keys` puis `1 for airy`. Acceptez ensuite tout ce qu'on pourrait vous demander, notamment à la fin de la procédure vous êtes invité à éxécuter :

```
source ~/.bashrc
```

# Utilisation du simulateur