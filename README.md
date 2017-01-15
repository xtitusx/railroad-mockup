# RailRoadMockup Project


## Installation de l'environnement de développement
 
 1. Installer WebStorm 2016
 
 On va travailler dans le même environnement si possible pour simplifier. Donc il faut récupérer WebStorm sur le site d'IntelliJ, on a accès à une licence gratuite comme on est étudiant...
 
 2. Installer le projet dans WebStorm 
 
 Récupérer ce projet directement sur le site GitHub ou avec les commandes git, pour info c'est un projet Node.js Express. Pour tester le projet côté client, il faut utiliser Chrome (http://localhost:80), j'ai un bug mineur d'affichage de l'image sur les autres navigateurs (lié au "Content-Type": "text/html" j'imagine), mais on s'en fiche.


## Explications

Structure du projet

* bin/www

C'est le point d'entrée du serveur (défini dans 'package.json'). C'et ici qu'est créé un objet 'server' à chaque connexion d'un client en prenant en compte les paramètres du fichier 'app.js'.
C'est ici que seront gérés les événements liés au socket, c'est à dire les échanges bidirectionnels avec le client.

* app.js

C'est le descripteur de l'application (http://expressjs.com/fr/api.html), on définit ici le routage (avec **app.use()**, voir http://expressjs.com/fr/starter/basic-routing.html), et les variables globales de l'application (avec **app.set()**).
On initialise ici les objets métier (voir commentaire // Business Objects Collections), c'est à dire les objets liés au "monde réel" : les feux de signalisation, les aiguillages, etc...

* routes

Contient des fonctions dites middleware qui gèrent les requêtes et les réponses. Ca ressemble au Contrôleur dans un MVC classique.
On effectue un traitement et on renvoie une Vue. Ici on n'utilise pas les templates du répertoire views car on dessine directement en vectoriel dans un DOM virtuel.
La seule route qu'on utilise est "railroad.js" qui va créer des objets SVG côté serveur (node-raphael + jsdom) en lisant le fichier "data/mockup.json" et les envoyer à la fabrique pour les monter à la volée et les stocker dans une Collection pour les manipuler plus tard.

* business

Contient tous les objets métier, pour l'instant seul "trafficlight.js" (feu de signalisation...) est partiellement implémenté.

* models

Contient les fonctions d'accès à la source de donnée, en l'occurence l'arduino ("arduino.js" à implémenter).

* views

Système de template jade, on ne s'en servira pas.

* public/javascript

Contient les fonctions liées à la communication du socket, et les fonction propres du client (par exemple du raphael.js côté client, etc...). 

## Todo list

Liste des tâches que je vais répartir dans des tickets JIRA, vous choississez ce que vous voulez faire, possibilité de travailler en binôme :

1. Arduino (point bloquant) ASAP

* Chercher un module node.js pour attaquer l'arduino en USB, et l'implémenter dans "models/arduino.js". Il faut des fonctions pour récupérer l'état des équipements, et des fonctions pour les modifier.

* Bonus : Regarder si on peut utiliser une sorte de Singleton pour récupérer l'état des équipement, car imaginons qu'on est 50 clients connectés, on ne va pas faire 50 requêtes pour récupérer la même chose...

* Mapper les adresses HEX des équipements avec leur nom (ex : 1.1, 4.3). On peut aussi créer une fonction de génération d'addresse en fonction de la convention de nommage des équipements pour s'amuser...

2. Objet métier Trafficlight (feu de signalisation)

* Une fois l'arduino connecté, changer les ID du fichier "data/mockup.json" par les addresses HEX mappées.

* Finir d'implémenter "trafficlight.js" et adapter les événéments du socket (enlever le simulateur de changement de couleur sur le socket que j'ai fait pour tester).

* Voir s'il faut changer l'état d'un trafficlight à partir du client et l'implémenter côté client/serveur si besoin.

3. Objet métier Scanner (lecteur code barre) (A faire après 1. & 2.)

* Sur le même principe, implémenter l'objet.

4. Objet métier Switch (aiguillage) (A faire après 1. & 2.)

* Idem, attention il faudra sûrement créer les objets SVG sur le client, car on ne peut pas les animer dans un jsdom côté serveur avec node-raphael. A creuser.