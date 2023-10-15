Que se passe-t-il lorsque vous tapez "google.com" dans la barre d'adresse de votre navigateur et que vous appuyez sur "Entrée" ?

Ce dépôt est une tentative pour répondre à la vieille question d'entrevue : "Que se passe-t-il lorsque vous tapez 'google.com' dans la barre d'adresse de votre navigateur et appuyez sur 'Entrée' ?"

Mais au lieu de donner la réponse habituelle, nous allons essayer de répondre à cette question en détail. Rien ne sera omis.

C'est un processus collaboratif, alors plongez-y et essayez d'apporter votre aide ! De nombreux détails manquent encore et n'attendent que d'être ajoutés par vos soins ! N'hésitez pas à nous soumettre une demande d'extraction, s'il vous plaît !

Tout ceci est sous licence Creative Commons Zero.

Lisez ceci en chinois simplifié, en japonais, en coréen et en espagnol. REMARQUE : ces traductions n'ont pas été examinées par les responsables du référentiel alex/what-happens-when.

**Table des matières**
- Appui sur la touche "g"
- Enfoncement de la touche "Entrée"
- Déclenchement d'une interruption [NON pour les claviers USB]
- (Sous Windows) Envoi d'un message WM_KEYDOWN à l'application
- (Sous OS X) Envoi d'un événement KeyDown NSEvent à l'application
- (Sous GNU/Linux) Écoute des codes de touche par le serveur Xorg
- Analyse de l'URL
- URL ou terme de recherche ?
- Conversion des caractères Unicode non-ASCII dans le nom de domaine
- Vérification de la liste HSTS
- Recherche DNS
- Processus ARP
- Ouverture d'une prise
- Gestion de la poignée TLS
- En cas de perte d'un paquet
- Protocole HTTP
- Traitement des demandes par le serveur HTTP
- Dans les coulisses du navigateur
- Navigateur
- Analyse HTML
- Interprétation CSS
- Rendu de la page
- Rendu GPU
- Serveur de fenêtrage
- Post-rendu et interaction utilisateur induite

La touche "g" est pressée
Les sections suivantes expliquent les actions physiques du clavier et les interruptions du système d'exploitation. Lorsque vous appuyez sur la touche "g", le navigateur reçoit l'événement, et les fonctions d'auto-complétion entrent en jeu. Selon l'algorithme de votre navigateur et que vous soyez en mode privé/incognito ou non, diverses suggestions vous sont présentées dans la liste déroulante sous la barre d'URL. La plupart de ces algorithmes trient et priorisent les résultats en fonction de l'historique de recherche, des favoris, des cookies et des recherches populaires sur Internet dans son ensemble. Au fur et à mesure que vous tapez "google.com", de nombreux blocs de code s'exécutent, et les suggestions se peaufinent à chaque frappe. Il peut même suggérer "google.com" avant que vous n'ayez fini de le taper.

La touche "Entrée" est enfoncée
Pour choisir un point de départ, prenons la touche "Entrée" du clavier atteignant le bas de sa plage. À ce stade, un circuit électrique spécifique à la touche "Entrée" se ferme (directement ou par capacité). Cela permet à un faible courant de circuler dans la logique du clavier, qui analyse l'état de chaque commutateur de touche, supprime le bruit électrique résultant de la fermeture intermittente rapide du commutateur et le convertit en un code entier, dans ce cas, 13. Le contrôleur du clavier code ensuite la valeur du code pour la transmettre à l'ordinateur. Cela se fait presque universellement via une connexion USB (Universal Serial Bus) ou Bluetooth, mais historiquement cela se faisait via des connexions PS/2 ou ADB.

Dans le cas du clavier USB :
- La partie électronique USB du clavier est alimentée par une alimentation 5V fournie sur la broche 1 par le contrôleur hôte USB de l'ordinateur.
- La valeur du code générée est stockée dans la mémoire interne de la partie électronique du clavier dans un registre appelé "point de terminaison".
- Le contrôleur USB hôte interroge ce "point de terminaison" environ toutes les ~10ms (valeur minimale déclarée par le clavier), de manière à obtenir la valeur du code stockée.
- Cette valeur est transmise au moteur SIE USB (Serial Interface Engine) pour être convertie en un ou plusieurs paquets USB suivant le protocole USB de bas niveau.
- Ces paquets sont envoyés par un signal électrique différentiel sur les broches D+ et D- (les deux du milieu) à une vitesse maximale de 1,5 Mb/s, car un périphérique HID (Human Interface Device) est toujours déclaré comme un "périphérique basse vitesse" (compatible USB 2.0).
- Ce signal série est ensuite décodé par le contrôleur hôte USB de l'ordinateur et interprété par le pilote de périphérique clavier universel HID du clavier de l'ordinateur. La valeur de la touche est ensuite transmise à la couche d'abstraction matérielle du système d'exploitation.

Dans le cas d'un clavier virtuel (comme sur les appareils à écran tactile) :
- Lorsque l'utilisateur pose son doigt sur un écran tactile capacitif moderne, une petite quantité de courant est transférée au doigt. Cela complète le circuit à travers le champ électrostatique de la couche conductrice et crée une chute de tension à cet endroit sur l'écran. Le contrôleur de l'écran déclenche ensuite une interruption signalant les coordonnées de l'appui sur la touche.
- Ensuite, le système d'exploitation mobile notifie l'application actuellement au premier plan d'un événement de pression dans un de ses éléments GUI (qui est maintenant l'application du clavier virtuel boutons).
- Le clavier virtuel peut maintenant déclencher une interruption logicielle pour envoyer un message de "touche enfoncée" au système d'exploitation.
- Cette interruption notifie l'application actuellement au premier plan d'un évén

ement de "touche enfoncée".
- Interruption déclenchée [PAS pour les claviers USB]
- Le clavier envoie des signaux électriques pour signaler le relâchement de la touche "Entrée", ce qui signifie que le voyage de la touche a atteint le bas de sa plage et que le commutateur a rebondi pour reconnecter le circuit. Cela est reconnu par l'ordinateur en tant que 13, puis 0, par rapport à la pression initiale.

(Sous Windows) Un message WM_KEYDOWN est envoyé à l'application
Lorsque la touche "Entrée" est enfoncée, l'ordinateur (système d'exploitation) reçoit une interruption matérielle indiquant que la touche a été enfoncée. Le système d'exploitation, qui gère les entrées et les sorties de l'ordinateur, est ensuite informé que la touche "Entrée" a été enfoncée. Pour Windows, ce processus entraîne l'envoi d'un message WM_KEYDOWN (un message Windows) à l'application active.

(Sous OS X) Un événement KeyDown NSEvent est envoyé à l'application
Sur macOS, un événement KeyDown NSEvent est généré lorsque la touche "Entrée" est enfoncée. Cet événement est ensuite envoyé à l'application active.

(Sous GNU/Linux) le serveur Xorg écoute les codes de touche
Sous les systèmes GNU/Linux, le serveur Xorg écoute les codes de touche provenant du clavier. Lorsque la touche "Entrée" est enfoncée, un événement correspondant est généré et envoyé au gestionnaire de fenêtres ou à l'application active.

Ces étapes décrivent le processus de base de ce qui se passe lorsque vous appuyez sur la touche "Entrée" après avoir saisi "google.com" dans la barre d'adresse de votre navigateur. La suite de la réponse détaille davantage les étapes suivantes du processus de navigation, y compris l'analyse de l'URL, la recherche DNS, le processus ARP, l'ouverture d'une prise, etc.
