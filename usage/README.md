# RoverC'S'oftware : utilisation du buggy

Cette partie se consacre à l'utilisation du buggy dans le cadre d'un développement pour les différents modes d'usage.

## Utilisation du simulateur

Dans cette partie on s'intéresse au simulateur disponible pour tester le buggy dans un environnement virtuel.

Une fois que vous avez modifié (ou non) le code du **Cerebri** ou de **Cranium**, vous devez à nouveau compiler les softs sur votre image **Docker**, en effectuant ces commandes :

*Pour rappel, pour vous rendre dans l'image Docker vous devez éxécuter cela :*

````
cd ~/cognipilot/docker/dream
./dream start
./dream exec
````



````
cd ~/cognipilot/cranium/
colcon build --symlink-install
cd ~/cognipilot/cranium/
source install/setup.bash
````

````
cd ~/cognipilot/ws/cerebri
west update
west build -b native_sim app/b3rb/ -p -t install
source ~/.bashrc
````

Une fois tout recompiler il suffit d'éxécuter la commande :

````
ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_raceway_octagon
````

puis d'ouvrir un autre terminal, et de faire

````
cd ~/cognipilot/docker/dream
./dream start
./dream exec
````

puis

````
ros2 launch electrode electrode.launch.py sim:=true 
````

- Une fenêtre devrez alors s'ouvrir, vous devez cliquer sur "Ouvrir une connexion" et acceptez la connexion "ws://localhost:8765"
- Ensuite dans la fenêtre principale, tout en haut à gauche cliquez et sélectionnez "View" puis "Import layout from file"
- Sélectionnez alors cognipilot > electrode > src > electrode > foxglove_layouts > b3rb.json

Le monde **nxp_raceway_octagon** peut être remplacé par un des monde suivant :

- depot
- basic_map

## Contrôle du buggy via l'interface Electrode

Vous pouvez contrôler le buggy manuellement et déclencher le mode automatique de suivi de ligne une fois bien placé. Pour faire ça, il faut `ssh`dans la **NavQ+** :

````
ssh user@10.42.0.40
````

Ensuite il faut lancer le programme principal, qui est un **noeud** `ROS2`,

````
ros2 launch b3rb_bringup robot.launch.py
````

Ensuite il faut ouvrir un autre terminal et pénétrer l'image **Docker**

````
cd ~/cognipilot/docker/dream
./dream start
./dream exec
````

Et enfin démarer **Electrode**

````
ros2 launch electrode electrode.launch.py sim:=true 
````