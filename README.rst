Ce dépôt est la version française de la ressource d’Alex Waynor, "Que se passe-t-il quand…".

Alex Waynor n’est pas associé à ce travail et n’a pas révisé la traduction.
====================

Ce dépôt est une tentative de répondre à l’éternelle question d’entretien :

"Que se passe-t-il lorsque vous tapez google.com dans la barre d’adresse de votre navigateur et appuyez sur Entrée ?"

Sauf qu’au lieu de raconter l’histoire habituelle, nous allons essayer de répondre à cette question avec le plus de détails possible. Rien ne sera omis.

Il s’agit d’un processus collaboratif, alors n’hésitez pas à participer et à apporter votre aide ! Il manque encore énormément de détails, qui n’attendent que vous pour être ajoutés. Alors, envoyez-nous une pull request, s’il vous plaît !

Tout ce contenu est publié sous les termes de la licence `Creative Commons Zero`.

Lisez ceci en `简体中文` (chinois simplifié), `日本語` (japonais), `한국어` (coréen) et `Spanish` (espagnol). REMARQUE : ces versions n’ont pas été révisées par les mainteneurs d’alex/what-happens-when.


Table des matières
====================

.. contents::
   :backlinks: none
   :local:

La touche "g" est enfoncée
----------------------
Les sections suivantes expliquent les actions physiques du clavier ainsi que les interruptions du système d’exploitation. Lorsque vous appuyez 
sur la touche "g", le navigateur reçoit l’événement et les fonctions d’auto-complétion se déclenchent. Selon l’algorithme de votre navigateur 
et selon que vous soyez ou non en mode privé/incognito, différentes suggestions vous seront présentées dans le menu déroulant situé sous la barre d’URL.
 La plupart de ces algorithmes trient et priorisent les résultats en fonction de l’historique de recherche, des favoris, des cookies et des 
recherches populaires sur Internet dans son ensemble.

Pendant que vous tapez "google.com", de nombreux blocs de code s’exécutent et les suggestions sont affinées à chaque frappe de touche. Il est 
même possible que "google.com" vous soit suggéré avant que vous n’ayez fini de le taper.

La touche "Entrée" est enfoncée à fond
---------------------------

Pour choisir un point de départ, prenons le moment où la touche Enter du clavier atteint le bas de sa course. À cet instant, un circuit électrique spécifique à la touche Enter se ferme (soit directement, soit de manière capacitive). Cela permet à une petite quantité de courant de circuler dans les circuits logiques du clavier, qui analysent l’état de chaque interrupteur de touche, éliminent le bruit électrique causé par les fermetures rapides et intermittentes de l’interrupteur, puis le convertissent en un entier de type keycode, dans ce cas précis, 13.

Le contrôleur du clavier encode ensuite le keycode pour le transporter vers l’ordinateur. Aujourd’hui, cela se fait presque universellement via une connexion Universal Serial Bus (USB) ou Bluetooth, mais historiquement, cela se faisait via des connexions PS/2 ou ADB.

*Dans le cas d’un clavier USB :*

* Les circuits USB du clavier sont alimentés par l’alimentation 5V fournie via la broche 1 du contrôleur hôte USB de l’ordinateur.

* Le keycode généré est stocké par les circuits internes du clavier dans un registre appelé "endpoint".

* Le contrôleur hôte USB interroge cet "endpoint" toutes les ~10 ms (valeur minimale déclarée par le clavier), ce qui lui permet de récupérer la valeur du keycode qui y est stockée.

* Cette valeur est envoyée au USB SIE (Serial Interface Engine) afin d’être convertie en un ou plusieurs paquets USB respectant le protocole USB de bas niveau.

* Ces paquets sont transmis par un signal électrique différentiel via les broches D+ et D- (les deux du milieu), à une vitesse maximale de 1,5 Mb/s, car un périphérique HID (Human Interface Device) est toujours déclaré comme un "low-speed device" (conformité USB 2.0).

* Ce signal série est ensuite décodé par le contrôleur hôte USB de l’ordinateur, puis interprété par le pilote universel de clavier Human Interface Device (HID) de l’ordinateur. La valeur de la touche est ensuite transmise à la couche d’abstraction matérielle du système d’exploitation.

*Dans le cas d’un clavier virtuel (comme sur les appareils à écran tactile) :*

* Lorsqu’un utilisateur pose son doigt sur un écran tactile capacitif moderne, une infime quantité de courant est transférée au doigt. Cela complète le circuit à travers le champ électrostatique de la couche conductrice et crée une chute de tension à cet endroit de l’écran. Le `screen controller` déclenche ensuite une interruption signalant les coordonnées de la pression sur la touche.

* Ensuite, le système d’exploitation mobile notifie l’application actuellement active d’un événement d’appui sur l’un de ses éléments d’interface graphique (GUI), qui correspond ici aux boutons de l’application de clavier virtuel.

* Le clavier virtuel peut alors déclencher une interruption logicielle pour envoyer un message de type 'key pressed' au système d’exploitation.

* Cette interruption informe l’application actuellement active d’un événement de type 'key pressed'.

Interrupt fires [NOT for USB keyboards]

---------------------------------------

Le clavier envoie des signaux sur sa ligne de requête d’interruption (IRQ), laquelle est associée à un `vecteur d’interruption` (entier) par le contrôleur d’interruptions. Le CPU utilise la `Interrupt Descriptor Table` (IDT) pour associer les vecteurs d’interruption à des fonctions (`interrupt handlers`) fournies par le noyau. Lorsqu’une interruption survient, le CPU consulte l’IDT à l’aide du vecteur d’interruption et exécute le gestionnaire approprié. Ainsi, le noyau est sollicité.

(Sous Windows) Un message `WM_KEYDOWN` est envoyé à l’application.

--------------------------------------------------------

Le transport HID transmet l’événement d’appui sur la touche au pilote `KBDHID.sys` qui convertit l’usage HID en un scancode. Dans ce cas, le scan code est `VK_RETURN` (`0x0D`). Le pilote `KBDHID.sys` interagit avec `KBDCLASS.sys` (pilote de classe clavier). Ce pilote est responsable du traitement sécurisé de toutes les entrées clavier et pavé numérique. Il appelle ensuite `Win32K.sys` (après avoir éventuellement fait passer le message à travers des filtres clavier tiers installés). Tout cela se produit en mode noyau.

`Win32K.sys` détermine quelle fenêtre est la fenêtre active via l’API `GetForegroundWindow()`. Cette API fournit le handle de fenêtre de la barre d’adresse du navigateur. La principale "message pump" de Windows appelle ensuite `SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)`. `lParam` est un masque de bits indiquant des informations supplémentaires sur la pression de touche : le nombre de répétitions (0 dans ce cas), le scan code réel (peut dépendre du constructeur OEM, mais ce ne serait généralement pas le cas pour `VK_RETURN`), si des touches étendues (par exemple alt, shift, ctrl) ont également été pressées (ce n’était pas le cas), ainsi que d’autres états.

L’API Windows `SendMessage` est une fonction simple qui ajoute le message à une file d’attente pour le handle de fenêtre particulier (`hWnd`). Plus tard, la fonction principale de traitement des messages (appelée `WindowProc`) assignée à `hWnd` est appelée afin de traiter chaque message dans la file d’attente.

La fenêtre (`hWnd`) active est en réalité un contrôle d’édition et le `WindowProc` dans ce cas possède un gestionnaire de messages pour les messages `WM_KEYDOWN`. Ce code examine le 3e paramètre transmis à `SendMessage` (`wParam`) et, comme il s’agit de `VK_RETURN`, sait que l’utilisateur a appuyé sur la touche ENTER.

(Sous OS X) Un `KeyDown` NSEvent est envoyé à l’application

--------------------------------------------------

Le signal d’interruption déclenche un événement d’interruption dans le pilote clavier kext de l’I/O Kit. Le pilote traduit le signal en un code de touche qui est transmis au processus `WindowServer` de OS X. En conséquence, le `WindowServer` envoie un événement à toute application appropriée (par exemple active ou en écoute) via son port Mach, où il est placé dans une file d’événements. Les événements peuvent ensuite être lus depuis cette file par des threads disposant des privilèges suffisants en appelant la fonction `mach_ipc_dispatch`. Cela se produit le plus souvent via, et est géré par, une boucle principale d’événements `NSApplication`, au moyen d’un `NSEvent` de type `NSEventType` `KeyDown`.

(Sous GNU/Linux) le serveur Xorg écoute les keycodes

---------------------------------------------------

Lorsqu’un `X server` graphique est utilisé, `X` utilise le pilote d’événements générique `evdev` pour récupérer la pression de touche. Un remappage des keycodes vers des scancodes est effectué à l’aide de keymaps et de règles spécifiques au `X server`.

Une fois le mappage des scancodes de la touche pressée terminé, le `X server` envoie le caractère au `window manager` (DWM, metacity, i3, etc), puis le `window manager` transmet à son tour le caractère à la fenêtre active.

L’API graphique de la fenêtre qui reçoit le caractère affiche le symbole de police approprié dans le champ actuellement sélectionné.

Analyser l’URL

---------

* Le navigateur dispose maintenant des informations suivantes contenues dans l’URL (Uniform Resource Locator) :

  * `Protocol`  "http"
    Utilise le 'Hyper Text Transfer Protocol'

  * `Resource`  "/"
    Récupère la page principale (index)

Est-ce une URL ou un terme de recherche ?

-----------------------------
Lorsqu’aucun protocole ou nom de domaine valide n’est fourni, le navigateur transmet le texte saisi dans la barre d’adresse au moteur de recherche web par défaut du navigateur. Dans de nombreux cas, une portion spéciale de texte est ajoutée à l’URL afin d’indiquer au moteur de recherche qu’elle provient de la barre d’URL d’un navigateur particulier.

Conversion des caractères Unicode non-ASCII dans le nom d’hôte
----------------------------------

* Le navigateur vérifie si le nom d’hôte contient des caractères qui ne font pas partie de `a-z`,
  `A-Z`, `0-9`, `-`, ou `.`.
* Comme le nom d’hôte est `google.com`, il n’y en aura pas, mais si c’était le cas, le navigateur appliquerait l’encodage `Punycode`_ à la partie nom d’hôte de l’URL.

Vérification de la liste HSTS
----------------------------------

* Le navigateur vérifie sa liste "préchargée HSTS (HTTP Strict Transport Security)". Il s’agit d’une liste de sites web qui ont demandé à être contactés uniquement via HTTPS.
* Si le site web figure dans cette liste, le navigateur envoie sa requête via HTTPS au lieu de HTTP. Sinon, la requête initiale est envoyée via HTTP.
  (Note : un site web peut toujours utiliser la politique HSTS *sans* être dans la liste HSTS. La première requête HTTP vers le site par un utilisateur peut recevoir une réponse demandant d’utiliser uniquement HTTPS. Cependant, cette unique requête HTTP pourrait potentiellement exposer l’utilisateur à une `downgrade attack`_, c’est pourquoi la liste HSTS est incluse dans les navigateurs modernes.)

Recherche DNS
----------------------------------

* Le navigateur vérifie si le domaine est dans son cache. (pour voir le cache DNS dans Chrome, allez sur `chrome://net-internals/#dns <chrome://net-internals/#dns>`_).
* Sinon, le navigateur appelle la fonction de bibliothèque `gethostbyname` (varie selon l’OS) pour effectuer la résolution.
* `gethostbyname` vérifie si le nom d’hôte peut être résolu via le fichier local `hosts` (dont l’emplacement `varie selon l’OS`_) avant de tenter une résolution DNS.
* Si `gethostbyname` ne trouve ni cache ni entrée dans le fichier `hosts`, il envoie une requête au serveur DNS configuré dans la pile réseau. Il s’agit généralement du routeur local ou du serveur DNS cache de l’ISP.
* Si le serveur DNS est sur le même sous-réseau, la bibliothèque réseau suit le processus `ARP` ci-dessous pour le serveur DNS.
* Si le serveur DNS est sur un sous-réseau différent, la bibliothèque réseau suit le processus `ARP` ci-dessous pour l’adresse IP de la passerelle par défaut.

Processus ARP
----------------------------------

Afin d’envoyer une requête ARP (Address Resolution Protocol), la bibliothèque de pile réseau doit disposer de l’adresse IP cible à résoudre. Elle doit également connaître l’adresse MAC de l’interface utilisée pour diffuser la requête ARP.

Le cache ARP est d’abord vérifié pour une entrée ARP correspondant à notre IP cible. Si elle est présente dans le cache, la fonction de bibliothèque retourne le résultat : IP cible = MAC.

Si l’entrée n’est pas dans le cache ARP :

* La table de routage est consultée afin de voir si l’adresse IP cible se trouve sur l’un des sous-réseaux de la table locale. Si oui, la bibliothèque utilise l’interface associée à ce sous-réseau. Sinon, elle utilise l’interface correspondant au sous-réseau de la passerelle par défaut.

* L’adresse MAC de l’interface réseau sélectionnée est recherchée.

* La bibliothèque réseau envoie une requête ARP de couche 2 (couche liaison de données du modèle `OSI model`_) :

`ARP Request`::

```
Sender MAC: interface:mac:address:here  
Sender IP: interface.ip.goes.here  
Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)  
Target IP: target.ip.goes.here  
```

Selon le type de matériel entre l’ordinateur et le routeur :

Connexion directe :

* Si l’ordinateur est directement connecté au routeur, le routeur répond avec une `ARP Reply` (voir ci-dessous)

Hub :

* Si l’ordinateur est connecté à un hub, le hub diffuse la requête ARP sur tous les autres ports. Si le routeur est connecté sur le même "wire", il répond avec une `ARP Reply` (voir ci-dessous).

Switch :

* Si l’ordinateur est connecté à un switch, le switch vérifie sa table CAM/MAC locale pour voir quel port correspond à l’adresse MAC recherchée. Si le switch n’a aucune entrée pour cette MAC, il rediffuse la requête ARP sur tous les autres ports.

* Si le switch a une entrée dans la table MAC/CAM, il envoie la requête ARP uniquement au port correspondant à l’adresse MAC recherchée.

* Si le routeur est sur le même "wire", il répond avec une `ARP Reply` (voir ci-dessous)

`ARP Reply`::

```
Sender MAC: target:mac:address:here  
Sender IP: target.ip.goes.here  
Target MAC: interface:mac:address:here  
Target IP: interface.ip.goes.here  
```

Maintenant que la bibliothèque réseau possède l’adresse IP soit de notre serveur DNS soit de la passerelle par défaut, elle peut reprendre le processus DNS :

* Le client DNS établit une socket vers le port UDP 53 du serveur DNS, en utilisant un port source supérieur à 1023.
* Si la taille de la réponse est trop grande, TCP sera utilisé à la place.
* Si le serveur DNS local/ISP ne possède pas la réponse, une recherche récursive est demandée, remontant la liste des serveurs DNS jusqu’à atteindre le SOA, et si une réponse est trouvée, elle est renvoyée.

Ouverture d’une socket
----------------------------------

Une fois que le navigateur reçoit l’adresse IP du serveur de destination, il utilise cette information et le numéro de port fourni dans l’URL (le protocole HTTP utilise par défaut le port 80, et HTTPS le port 443), et appelle la fonction système nommée `socket` afin de demander une socket TCP de type flux — `AF_INET/AF_INET6` et `SOCK_STREAM`.

* Cette requête est d’abord transmise à la couche transport où un segment TCP est créé. Le port de destination est ajouté à l’en-tête, et un port source est choisi dans la plage dynamique du noyau (ip_local_port_range sous Linux).
* Ce segment est envoyé à la couche réseau, qui ajoute un en-tête IP supplémentaire. L’adresse IP du serveur cible ainsi que celle de la machine actuelle sont insérées pour former un paquet.
* Le paquet arrive ensuite à la couche liaison. Un en-tête de trame est ajouté, incluant l’adresse MAC de la carte réseau ainsi que celle de la passerelle (routeur local). Comme précédemment, si le noyau ne connaît pas l’adresse MAC de la passerelle, il doit diffuser une requête ARP pour la trouver.

À ce stade, le paquet est prêt à être transmis via soit :

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

Pour la plupart des connexions Internet domestiques ou de petites entreprises, le paquet passe de votre ordinateur, éventuellement à travers un réseau local, puis à travers un modem (MOdulator/DEModulator) qui convertit les 1 et 0 numériques en un signal analogique adapté à la transmission via des connexions téléphoniques, câble ou téléphonie sans fil. À l’autre extrémité de la connexion se trouve un autre modem qui reconvertit le signal analogique en données numériques afin qu’elles puissent être traitées par le prochain `network node`_ où les adresses source et destination seront analysées plus en détail.

Les connexions plus importantes (grandes entreprises) et certaines connexions résidentielles récentes utilisent la fibre ou une connexion Ethernet directe, auquel cas les données restent numériques et sont transmises directement au prochain `network node`_ pour traitement.

Finalement, le paquet atteint le routeur qui gère le sous-réseau local. À partir de là, il continue de voyager vers les routeurs de bordure du système autonome (AS), d’autres AS, et enfin vers le serveur de destination. Chaque routeur sur le chemin extrait l’adresse de destination de l’en-tête IP et l’achemine vers le prochain saut approprié. Le champ Time To Live (TTL) de l’en-tête IP est décrémenté de 1 à chaque routeur traversé. Le paquet est supprimé si le champ TTL atteint zéro ou si le routeur courant n’a plus de place dans sa file d’attente (par exemple en cas de congestion réseau).

Cet envoi et cette réception se produisent plusieurs fois selon le flux de connexion TCP :

* Le client choisit un numéro de séquence initial (ISN) et envoie le paquet au serveur avec le bit SYN activé pour indiquer qu’il initialise l’ISN
* Le serveur reçoit le SYN et, s’il est en accord :

  * Le serveur choisit son propre numéro de séquence initial
  * Le serveur active SYN pour indiquer qu’il choisit son ISN
  * Le serveur copie le (ISN client + 1) dans son champ ACK et ajoute le drapeau ACK pour indiquer qu’il accuse réception du premier paquet
* Le client accuse réception de la connexion en envoyant un paquet :

  * Il augmente son propre numéro de séquence
  * Il augmente le numéro d’acquittement du récepteur
  * Il active le champ ACK
* Les données sont transférées comme suit :

  * À mesure qu’un côté envoie N octets de données, il augmente son SEQ de cette valeur
  * Lorsque l’autre côté accuse réception de ces paquets (ou d’une série de paquets), il envoie un paquet ACK dont la valeur ACK correspond au dernier SEQ reçu
* Pour fermer la connexion :

  * Le côté initiateur envoie un paquet FIN
  * L’autre côté accuse réception du FIN et envoie son propre FIN
  * L’initiateur accuse réception du FIN reçu avec un ACK

TLS handshake
----------------------------------

* Le client envoie un message `ClientHello` au serveur avec la version TLS, la liste des algorithmes de chiffrement et les méthodes de compression disponibles

* Le serveur répond avec un message `ServerHello` contenant la version TLS, le chiffrement sélectionné, la compression choisie et le certificat public du serveur signé par une CA (Certificate Authority). Le certificat contient une clé publique utilisée par le client pour chiffrer le reste de la négociation jusqu’à ce qu’une clé symétrique soit établie

* Le client vérifie le certificat numérique du serveur via sa liste de CA de confiance. Si la confiance est établie, le client génère une suite d’octets pseudo-aléatoires et les chiffre avec la clé publique du serveur. Ces octets servent à dériver la clé symétrique

* Le serveur déchiffre ces octets avec sa clé privée et les utilise pour générer sa propre copie de la clé maîtresse symétrique

* Le client envoie un message `Finished` au serveur, en chiffrant un hash de la communication jusqu’à ce point avec la clé symétrique

* Le serveur génère son propre hash, puis déchiffre celui envoyé par le client pour vérification. S’ils correspondent, il envoie à son tour un message `Finished` chiffré avec la clé symétrique

* À partir de ce moment, la session TLS transmet les données applicatives (HTTP) chiffrées avec la clé symétrique convenue

Si un paquet est perdu
----------------------------------

Parfois, à cause de la congestion réseau ou de connexions matérielles instables, des paquets TLS sont perdus avant d’atteindre leur destination finale. L’émetteur doit alors décider comment réagir. L’algorithme utilisé est appelé `TCP congestion control`*. Cela varie selon le système ; les plus courants sont `cubic`* sur les systèmes récents et `New Reno`_ sur la plupart des autres

* Le client choisit une `congestion window`_ basée sur la `maximum segment size`_ (MSS) de la connexion
* Pour chaque paquet accusé de réception, la fenêtre double jusqu’à atteindre le seuil de slow-start
* Après ce seuil, la fenêtre augmente de manière additive pour chaque ACK reçu. Si un paquet est perdu, la fenêtre diminue de façon exponentielle jusqu’à nouvel acquittement

HTTP protocol
----------------------------------

Si le navigateur web utilisé est écrit par Google, au lieu d’envoyer une requête HTTP classique, il peut tenter de négocier une mise à niveau vers le protocole SPDY avec le serveur

Si le client utilise HTTP et ne supporte pas SPDY, il envoie une requête de la forme ::

```
GET / HTTP/1.1  
Host: google.com  
Connection: close  
[autres en-têtes]  
```

où `[autres en-têtes]` représente une série de paires clé-valeur séparées par deux-points, formatées selon la spécification HTTP et séparées par des retours à la ligne. (Cela suppose que le navigateur n’a pas de bug violant la spécification HTTP, et qu’il utilise HTTP/1.1, sinon certains en-têtes comme `Host` peuvent être absents.)

HTTP/1.1 définit l’option de connexion "close" pour signaler que la connexion sera fermée après la réponse

Exemple :

```
Connection: close  
```

Les applications HTTP/1.1 qui ne supportent pas les connexions persistantes doivent inclure cette option dans chaque message

Après l’envoi de la requête, le navigateur envoie une ligne vide indiquant la fin de la requête

Le serveur répond avec un code de statut suivi d’un en-tête de réponse :

```
200 OK
[entêtes de réponse]
```

Puis une ligne vide, suivie du contenu HTML de `www.google.com`. Le serveur peut ensuite fermer la connexion ou la maintenir ouverte selon les en-têtes

Si les en-têtes HTTP incluent suffisamment d’informations pour déterminer que le contenu mis en cache n’a pas changé (par exemple via `ETag`), le serveur peut répondre :

```
304 Non Modifié
[en-têtes de réponse] 
```

sans contenu, et le navigateur utilise son cache

Après analyse du HTML, le navigateur répète ce processus pour chaque ressource (image, CSS, favicon.ico, etc.) référencée

Si une ressource provient d’un autre domaine que `www.google.com`, le navigateur recommence le processus de résolution DNS pour ce domaine

Gestion des requêtes du serveur HTTP
----------------------------------

Le serveur HTTPD (HTTP Daemon) est celui qui gère les requêtes côté serveur. Les plus courants sont Apache ou nginx sous Linux, et IIS sous Windows

* Le serveur HTTPD reçoit la requête
* Le serveur décompose la requête en paramètres :

  * Méthode HTTP (`GET`, `HEAD`, `POST`, etc.) — ici `GET`
  * Domaine : google.com
  * Chemin demandé : /
* Le serveur vérifie la configuration Virtual Host correspondant à google.com
* Il vérifie que GET est autorisé
* Il vérifie les permissions du client
* Il applique éventuellement des règles de réécriture (mod_rewrite, etc.)
* Il récupère le contenu correspondant, ici le fichier index
* Il interprète le fichier via le handler approprié (PHP, etc.) et envoie la sortie au client

Dans les coulisses du navigateur

----------------------------------

Une fois que le serveur fournit les ressources (HTML, CSS, JS, images, etc.) au navigateur, le processus suivant a lieu :

* Analyse - HTML, CSS, JS
* Rendu - Construction de l'arborescence DOM → Arborescence de rendu → Mise en page de l'arborescence de rendu → Peinture de l'arborescence de rendu

navigateur
-------

La fonctionnalité du navigateur est de présenter la ressource web que vous choisissez, en la demandant au serveur et en l'affichant dans la fenêtre du navigateur. La ressource est généralement un document HTML, mais il peut aussi s'agir d'un PDF, d'une image ou d'un autre type de contenu. L'emplacement de la ressource est spécifié par l'utilisateur à l'aide d'un URI (Identifiant Uniforme de Ressource).

La façon dont le navigateur interprète et affiche les fichiers HTML est spécifiée dans les spécifications HTML et CSS. Ces spécifications sont maintenues par l'organisation W3C (World Wide Web Consortium), qui est l'organisme de normalisation pour le web.

Les interfaces utilisateur des navigateurs ont beaucoup de points communs. Parmi les éléments d'interface utilisateur courants, on trouve :

* Une barre d'adresse pour insérer un URI
* Des boutons de retour et d'avance
* Des options de favoris
* Des boutons de rafraîchissement et d'arrêt pour rafraîchir ou arrêter le chargement des documents en cours
* Un bouton d'accueil qui vous emmène à votre page d'accueil

**Structure de haut niveau du navigateur**

Les composants des navigateurs sont :

* **Interface utilisateur :** L'interface utilisateur comprend la barre d'adresse, le bouton retour/avance, le menu des favoris, etc. Chaque partie de l'affichage du navigateur à l'exception de la fenêtre où vous voyez la page demandée.
* **Moteur du navigateur :** Le moteur du navigateur coordonne les actions entre l'interface utilisateur et le moteur de rendu.
* **Moteur de rendu :** Le moteur de rendu est responsable de l'affichage du contenu demandé. Par exemple, si le contenu demandé est du HTML, le moteur de rendu analyse le HTML et le CSS, et affiche le contenu analysé à l'écran.
* **Réseau :** Le réseau gère les appels réseau comme les requêtes HTTP, en utilisant différentes implémentations pour différentes plateformes derrière une interface indépendante de la plateforme.
* **Backend de l'interface utilisateur :** Le backend de l'interface utilisateur est utilisé pour dessiner des widgets de base comme les listes déroulantes et les fenêtres. Ce backend expose une interface générique qui n'est pas spécifique à une plateforme. En dessous, il utilise les méthodes d'interface utilisateur du système d'exploitation.

Analyse Html
------------

Le moteur de rendu commence à récupérer le contenu du document demandé depuis la couche réseau. Cela se fait généralement par morceaux de 8 Ko.

Le rôle principal du parseur HTML est d’analyser le balisage HTML pour en faire un arbre de parsing.

L’arbre produit (l’« arbre de parsing ») est un arbre de nœuds d’éléments DOM et d’attributs. DOM est l’abréviation de Document Object Model. C’est la représentation objet du document HTML et l’interface des éléments HTML vers le monde extérieur, comme JavaScript. La racine de l’arbre est l’objet « Document ». Avant toute manipulation via des scripts, le DOM a une relation presque un-à-un avec le balisage.

**L'algorithme d'analyse**

Le HTML ne peut pas être analysé en utilisant les analyseurs classiques descendents ou ascendants.  

Les raisons sont :  

* La nature permissive du langage.  
* Le fait que les navigateurs tolèrent traditionnellement les erreurs pour gérer des cas connus de HTML invalide.  
* Le processus d'analyse est réentrant. Pour d'autres langages, la source ne change pas pendant l'analyse, mais en HTML, du code dynamique (comme les éléments script contenant des appels à `document.write()`) peut ajouter des tokens supplémentaires, donc le processus d'analyse modifie en fait l'entrée.  

Impossible d'utiliser les techniques d'analyse classiques, le navigateur utilise donc un analyseur personnalisé pour analyser le HTML. L'algorithme d'analyse est décrit en détail dans la spécification HTML5.  

L'algorithme se compose de deux étapes : la tokenisation et la construction de l'arbre.

**Actions lorsque l'analyse est terminée**

Le navigateur commence à récupérer les ressources externes liées à la page (CSS, images, fichiers JavaScript, etc.).

À ce stade, le navigateur marque le document comme interactif et commence à analyser les scripts en mode « différé » : ceux qui doivent être exécutés après que le document a été analysé. L'état du document est défini sur « complet » et un événement « load » est déclenché.

Notez qu'il n'y a jamais d'erreur « Syntaxe invalide » sur une page HTML. Les navigateurs corrigent tout contenu invalide et continuent.

Interprétation du CSS
------------------
* Analyser les fichiers CSS, <style>le contenu des balises « ''' et l’attribut « style »
valeurs utilisant « Grammaire lexicale et syntaxique CSS »'_
* Chaque fichier CSS est analysé en un « objet StyleSheet », où chaque objet
contient des règles CSS avec des sélecteurs et des objets correspondant à la grammaire CSS.
* Un analyseur CSS peut être descendant ou ascendant lorsqu’un générateur de parseurs spécifique est
est utilisé.

Rendu de page
--------------

* Créez un « arbre de cadre » ou « arbre de rendu » en parcourant les nœuds du DOM et en calculant les valeurs de style CSS pour chaque nœud.  
* Calculez la largeur préférée de chaque nœud dans l’« arbre de cadre » de bas en haut en additionnant la largeur préférée des nœuds enfants ainsi que les marges, bordures et padding horizontaux du nœud.  
* Calculez la largeur réelle de chaque nœud de haut en bas en répartissant la largeur disponible de chaque nœud à ses enfants.  
* Calculez la hauteur de chaque nœud de bas en haut en appliquant le retour à la ligne du texte et en sommant les hauteurs des nœuds enfants et les marges, bordures et padding du nœud.  
* Calculez les coordonnées de chaque nœud en utilisant les informations calculées ci-dessus.  
* Des étapes plus compliquées sont nécessaires lorsque les éléments sont
 « floatés », positionnés « absolument » ou « relativement », ou quand d’autres fonctionnalités complexes sont utilisées. Voir http://dev.w3.org/csswg/css2/ et http://www.w3.org/Style/CSS/current-work pour plus de détails.
* Créez des calques pour décrire quelles parties de la page peuvent être animées en groupe sans être re-rasterisées. Chaque objet de cadre/rendu est assigné à un calque.
* Les textures sont allouées pour chaque calque de la page.
* Les objets de cadre/rendu pour chaque calque sont parcourus et les commandes de dessin sont exécutées pour leur calque respectif. Cela peut être rasterisé par le CPU ou dessiné directement sur le GPU en utilisant D2D/SkiaGL.
* Toutes les étapes ci-dessus peuvent réutiliser les valeurs calculées lors de la dernière fois que la page web a été rendue, de sorte que les modifications incrémentielles nécessitent moins de travail.
* Les calques de la page sont envoyés au processus de composition où ils sont combinés avec les calques d'autres contenus visibles comme l'interface du navigateur, les iframes et les panneaux d'extension.
* Les positions finales des calques sont calculées et les commandes composites sont envoyées
via Direct3D/OpenGL. Les tampons de commandes GPU sont vidés vers le GPU pour
le rendu asynchrone et l’image est envoyée au serveur de fenêtres.

Rendu GPU
-------------

* Pendant le processus de rendu, les couches de calcul graphique peuvent également utiliser le ``CPU`` général ou le processeur graphique ``GPU``.

* Lors de l'utilisation du ``GPU`` pour les calculs de rendu graphique, les couches logicielles graphiques divisent la tâche en plusieurs parties, afin de profiter du parallélisme massif du ``GPU`` pour les calculs en virgule flottante nécessaires au processus de rendu.

Serveur de fenêtres
-------------

Exécution après le rendu et déclenchée par l'utilisateur
-------------------------------------------------

Une fois le rendu terminé, le navigateur exécute du code JavaScript à cause de certains mécanismes de timing (comme une animation de Google 
Doodle) ou d'une interaction de l'utilisateur (taper une requête dans la barre de recherche et recevoir des suggestions). Des plugins comme 
Flash ou Java peuvent également s'exécuter, même si ce n'est pas le cas pour le moment sur la page d'accueil de Google. Les scripts peuvent 
aussi provoquer des requêtes réseau supplémentaires, ainsi que modifier la page ou sa disposition, entraînant un nouveau rendu et affichage de 
la page.



