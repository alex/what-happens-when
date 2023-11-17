What happens when...
====================

Ce référentiel est une tentative de répondre à la question d'entretien ancienne "Que se passe-t-il lorsque vous saisissez google.com dans la barre d'adresse de votre navigateur et appuyez sur Entrée ?"

Sauf qu'au lieu de l'histoire habituelle, nous allons essayer de répondre à cette question avec autant de détails que possible. Pas de sauts dans l'explication.

Il s'agit d'un processus collaboratif, alors plongez-vous et essayez de contribuer ! Il y a plein de détails manquants, simplement en attente que vous les ajoutiez ! Alors, envoyez-nous une demande de fusion, s'il vous plaît !

Tout cela est sous licence selon les termes de la licence Creative Commons Zero_.

Lisez ceci en `简体中文` (chinois simplifié), 日本語_ (japonais), `한국어`_
(coréen) et `Spanish`. REMARQUE : ces traductions n'ont pas été examinées par les mainteneurs de alex/what-happens-when.

Table des matières
====================

.. contents::
   :backlinks: none
   :local:

La touche "g" est enfoncée.
----------------------
Les sections suivantes expliquent les actions physiques du clavier et les interruptions du système d'exploitation. Lorsque vous appuyez sur la touche "g", le navigateur reçoit l'événement et les fonctions d'auto-complétion entrent en jeu. Selon l'algorithme de votre navigateur et que vous soyez en mode privé/incognito ou non, diverses suggestions vous seront présentées dans le menu déroulant situé sous la barre d'URL. La plupart de ces algorithmes trient et hiérarchisent les résultats en fonction de l'historique de recherche, des signets, des cookies et des recherches populaires sur Internet dans son ensemble. Pendant que vous tapez "google.com", de nombreux blocs de code s'exécutent et les suggestions seront affinées à chaque pression de touche. Il peut même suggérer "google.com" avant que vous ayez terminé de le taper.

La touche "Entrée" atteint le fond
---------------------------

Pour choisir un point zéro, choisissons la touche Entrée du clavier lorsque celle-ci atteint le bas de sa plage. À ce moment, un circuit électrique spécifique à la touche Entrée se ferme (directement ou capacitif). Cela permet à un faible courant de circuler dans la logique du clavier, qui analyse l'état de chaque interrupteur de touche, élimine le bruit électrique causé par la fermeture intermittente rapide de l'interrupteur, et le convertit en un entier de code de touche, dans ce cas le 13. Le contrôleur de clavier encode ensuite le code de touche pour le transporter vers l'ordinateur. Cela se fait presque universellement via une connexion Universal Serial Bus (USB) ou Bluetooth, mais historiquement cela se faisait via des connexions PS/2 ou ADB.

*Dans le cas du clavier USB :*

- La circuitrie USB du clavier est alimentée par l'alimentation de 5V fournie via la broche 1 par le contrôleur hôte USB de l'ordinateur.

- Le code de touche généré est stocké dans la mémoire interne du circuit du clavier dans un registre appelé "point de terminaison" (endpoint).

- Le contrôleur hôte USB interroge ce "point de terminaison" environ toutes les ~10ms (valeur minimale déclarée par le clavier), récupérant ainsi la valeur du code de touche qui y est stockée.

- Cette valeur passe par le SIE USB (Serial Interface Engine) pour être convertie en un ou plusieurs paquets USB suivant le protocole USB de bas niveau.

- Ces paquets sont envoyés par un signal électrique différentiel via les broches D+ et D- (les deux du milieu) à une vitesse maximale de 1,5 Mb/s, car un périphérique HID (Human Interface Device) est toujours déclaré comme un "périphérique basse vitesse" (conformité USB 2.0).

- Ce signal sériel est ensuite décodé par le contrôleur hôte USB de l'ordinateur et interprété par le pilote de périphérique universel de clavier HID de l'ordinateur. La valeur de la touche est ensuite transmise à la couche d'abstraction matérielle du système d'exploitation.

*Dans le cas du clavier virtuel (comme sur les appareils à écran tactile) :*

- Lorsque l'utilisateur pose son doigt sur un écran tactile capacitif moderne, une petite quantité de courant est transférée au doigt. Cela complète le circuit à travers le champ électrostatique de la couche conductrice et crée une chute de tension à ce point de l'écran. Le "contrôleur d'écran" déclenche alors une interruption signalant les coordonnées de la pression de la touche.

- Ensuite, le système d'exploitation mobile notifie l'application actuellement active d'un événement de pression dans l'un de ses éléments graphiques (qui est maintenant l'application du clavier virtuel).

- Le clavier virtuel peut maintenant déclencher une interruption logicielle pour envoyer un message 'touche enfoncée' à l'OS.

- Cette interruption notifie l'application actuellement active d'un événement 'touche enfoncée'.
