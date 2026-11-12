# PC Lights — Spécification et étude de faisabilité

Version : 0.2 — 12 novembre 2026. Statut : carte mère identifiée, compatibilité de contrôle à confirmer.

## 1. Conclusion et décision proposée

Une application Windows qui centralise les réglages RGB et démarre à l’ouverture de session est réalisable. Un dégradé très lent, avec une couleur différente par LED, est également réalisable **si les LED, le contrôleur, le câblage et son interface logicielle permettent tous un adressage individuel**.

La compatibilité de ce PC n’est pas encore établie : les marques seules ne suffisent pas. La première étape sera de documenter les références exactes et les branchements, avant tout essai RGB. L’effort principal concerne les intégrations matérielles et leur coexistence avec les logiciels existants ; le calcul du dégradé est relativement simple.

Recommandation : commencer par un simulateur et un clavier via une API officielle, puis intégrer l’éclairage du boîtier uniquement lorsqu’une voie de contrôle suffisamment documentée est identifiée. Conserver iCUE comme seul gestionnaire du matériel Corsair.

Le risque nul ne peut pas être garanti. Si le boîtier exige un protocole expérimental, un accès brut à la carte mère ou une modification de firmware, cette voie sera écartée du MVP. Il faudra alors accepter une couverture partielle ou étudier séparément un contrôleur adapté.

## 2. Besoin et périmètre

L’utilisateur souhaite une seule interface pour gérer progressivement les lumières du PC et du bureau, avec une attention prioritaire à la sécurité du matériel.

### MVP

- Application de bureau Windows, interface en français, icône dans la zone de notification.
- Démarrage automatique facultatif à l’ouverture de session et restauration du dernier profil validé.
- Contrôle du clavier Razer actuel, sous réserve du modèle et de ses capacités.
- Architecture permettant l’ajout du futur clavier ASUS, après vérification de sa référence et de son mode de connexion.
- Contrôle de l’éclairage des ventilateurs et du boîtier compatibles.
- Couleur fixe, extinction de l’éclairage, luminosité et dégradé animé très lent.
- Réglages par touche, LED ou zone selon les capacités réelles ; prévisualisation sans matériel.
- Profils locaux et commande pour suspendre le contrôle et rendre la main au gestionnaire précédent lorsque l’API le permet.

### Exclusions de la première version

- RAM, watercooling et autres périphériques Corsair : aucun contrôle ni changement de configuration iCUE.
- Vitesse des ventilateurs, pompe, courbes thermiques, tensions, overclocking et BIOS.
- Installation de firmware, reverse engineering sur le PC de l’utilisateur et écritures directes sur les bus système.
- Éclairage avant l’ouverture de session, synchronisation audio/vidéo et effets de jeux.
- Lumières connectées du bureau : prévues pour une seconde phase, après inventaire des marques et modèles.

Centraliser l’interface ne signifie pas forcément supprimer tous les services constructeurs : certaines API ont besoin de leur logiciel ou service en arrière-plan.

## 3. Inventaire et informations manquantes

| Élément | Information disponible | À confirmer |
| --- | --- | --- |
| Système | PC ; Windows retenu comme hypothèse | Version et édition de Windows |
| Clavier actuel | Razer | Modèle, disposition, RGB par touche ou par zone, USB/dongle/Bluetooth |
| Futur clavier | ASUS | Référence envisagée, API utilisable et limites selon la connexion |
| Boîtier | Cooler Master | Référence, ventilateurs inclus ou remplacés |
| Ventilateurs lumineux | Présents | Marque, modèle, quantité, nombre de LED et connectique |
| Carte mère | MSI MAG X870 TOMAHAWK WIFI, référence fournie par l’utilisateur | Révision et branchements RGB utilisés |
| Logiciel RGB du boîtier | MSI Center / Mystic Light probable, à confirmer | Nom exact et version |
| Contrôleur/hub | Inconnu | Référence, ports, branchement USB ou header, canaux indépendants ou dupliqués |
| Corsair | RAM et watercooling, iCUE satisfaisant | Éventuels partages de contrôleur ou intégrations avec les autres marques |
| Bureau | Lumières connectées | Marques, modèles, ponts éventuels et API locales |

La carte mère est une MSI MAG X870 TOMAHAWK WIFI sur plateforme AMD X870. La piste logicielle à examiner est donc MSI Mystic Light. MSI le propose notamment via MSI Center ; son utilisation actuelle sur ce PC reste une hypothèse à confirmer. [Présentation MSI Mystic Light](https://www.msi.com/Landing/mystic-light-rgb-gaming-pc/how-to)

La tentative de lecture de l’identité de la carte mère et de Windows par CIM a été refusée par l’environnement. La référence de carte mère provient de l’utilisateur. Aucun logiciel RGB n’a été lancé et aucune commande d’éclairage n’a été envoyée pour cette étude.

Pour compléter l’inventaire : références présentes sur les factures, informations système Windows (`msinfo32`) et documentation du boîtier/carte mère. L’inspection éventuelle des branchements se fera PC arrêté, alimentation coupée, sans déplacer de câble dans cette phase.

## 4. Conditions du contrôle LED par LED

Pour les connecteurs PC usuels, distinguer le RGB analogique 12 V, généralement à quatre broches, et l’ARGB adressable 5 V, généralement à trois broches. La documentation du modèle prévaut sur l’apparence du connecteur, notamment pour les connecteurs propriétaires.

| Configuration | Résultat possible |
| --- | --- |
| RGB analogique | Couleur commune au canal ; pas de dégradé spatial LED par LED |
| ARGB avec interface permettant des couleurs individuelles | Dégradé spatial et animation logicielle possibles |
| ARGB avec contrôleur limité à des effets prédéfinis | Effets disponibles uniquement ; animation personnalisée non garantie |
| Hub qui duplique le signal ARGB | Motif potentiellement individuel dans chaque ventilateur, mais répété sur les branches |
| Canaux indépendants ou chaîne réellement adressable | Possibilité de décaler les effets entre ventilateurs, selon les limites du contrôleur |

Un connecteur ARGB ne garantit donc pas à lui seul le rendu souhaité. Le logiciel ne peut pas rendre indépendantes des sorties câblées en parallèle. La longueur, l’ordre et les limites de courant doivent être tirés des notices, jamais déterminés en envoyant des valeurs au hasard.

Cooler Master décrit notamment un contrôleur Gen 2 permettant des couleurs individuelles dans MasterPlus+. Cela démontre une possibilité matérielle dans cette gamme, sans prouver que le boîtier actuel le possède ou qu’une API publique équivalente est disponible. [Documentation Cooler Master](https://files.coolermaster.com/landing/addressable-gen-2-rgb-led-controller/block1/html/index.html)

## 5. Faisabilité des intégrations

### Cas de la MSI MAG X870 TOMAHAWK WIFI

MSI indique trois connecteurs adressables `JARGB_V2` et un connecteur RGB `JRGB`. [Fiche technique du modèle exact](https://www.msi.com/Motherboard/MAG-X870-TOMAHAWK-WIFI/Specification)

Le manuel précise :

- `JARGB_V2_1~3` : ARGB 5 V, compatible Gen1/Gen2 ; jusqu’à 180 LED adressables et 3 A à 5 V par connecteur. Le nombre maximal de LED et la limite électrique doivent être respectés simultanément.
- `JRGB1` : RGB analogique 12 V, limite de 3 A ; ne permet pas de couleurs distinctes par LED.
- `JAF_2` : connecteur combiné EZ Conn-Header V2, pouvant aussi transporter l’ARGB ; à identifier si utilisé dans ce montage.

MSI avertit qu’un éclairage ARGB 5 V branché au JRGB 12 V serait endommagé. Aucun déplacement de câble n’est proposé à ce stade. [Manuel MSI, pages 52 à 55](https://download.msi.com/archive/mnu_exe/mb/MAGX870TOMAHAWKWIFI_English.pdf)

**Conséquence pour le projet :** la carte dispose bien de sorties adressables, ce qui satisfait une condition matérielle du dégradé recherché. Cela ne prouve ni que les ventilateurs y sont reliés, ni qu’une API expose un tableau de couleurs arbitraires par LED. Le branchement réel, un éventuel hub et la capacité de contrôle logiciel restent à qualifier.

La première piste sera une interface officielle MSI permettant de cibler uniquement les sorties concernées. Sa compatibilité avec cette carte et le contrôle individuel restent non validés. Les effets prédéfinis de Mystic Light ne suffisent pas à démontrer cette capacité. Aucun support OpenRGB ou LampArray n’est affirmé pour ce modèle dans cette étude ; aucune mise à jour BIOS/firmware n’est requise par la spec.

### Choix des interfaces

| Voie | Évaluation | Décision proposée |
| --- | --- | --- |
| Windows Dynamic Lighting / HID LampArray | Interface système adaptée aux appareils compatibles ; aucune compatibilité universelle par marque | Premier choix lorsque le modèle expose les capacités nécessaires |
| Razer Chroma SDK | Effets personnalisés pour clavier documentés ; modèle et environnement logiciel à valider | Premier prototype matériel si LampArray indisponible |
| ASUS Aura SDK | Contrôle de couleurs et disposition clavier documentés ; documentation en partie ancienne | Étude ciblée sur le futur modèle avant de promettre le support |
| SDK officiel du contrôleur du boîtier | Dépend du fabricant réel et des fonctions exposées | Voie privilégiée si elle permet de cibler uniquement le matériel autorisé |
| OpenRGB avec serveur SDK local | Couverture multimarque et accès depuis une application tierce | Option secondaire, désactivée par défaut, après analyse spécifique |
| Protocole matériel développé sur mesure | Maintenance et incertitude élevées | Hors MVP |

Windows permet le contrôle en arrière-plan de périphériques LampArray sous Windows 11, avec gestion de priorité entre applications. Cette voie exige une identité de package et l’extension d’application appropriée ; prévoir un packaging MSIX si elle est retenue. L’app doit respecter les pertes de disponibilité. [Documentation Microsoft](https://learn.microsoft.com/en-us/windows/apps/develop/devices-sensors/lighting-dynamic-lamparray)

Razer documente des effets personnalisés sur une matrice de clavier. Le prototype devra vérifier le mapping des touches, les dépendances Chroma/Synapse de l’installation et la portée des commandes : certaines opérations portent sur une catégorie d’appareils. [SDK clavier Razer](https://assets.razerzone.com/dev_portal/REST/html/md__r_e_s_t_external_03_keyboard.html)

ASUS documente une API locale pour lire la disposition et appliquer des couleurs, mais l’acquisition et la libération peuvent concerner plusieurs appareils Aura. La capacité à isoler le clavier et à préserver Corsair doit être démontrée. L’existence de cette documentation ne garantit pas la compatibilité d’un clavier récent ou de son mode sans fil. [Guide ASUS](https://www.asus.com/microsite/aurareadygamesdk/game_sdk_restful.html)

OpenRGB dispose d’un SDK réseau utilisable par une application tierce. Le client ne supprime cependant pas les risques du moteur matériel sous-jacent. Son README avertit explicitement de dommages ou blocages survenus par le passé avec certains matériels. Une présence dans la liste des appareils pris en charge ne constitue pas une garantie de sûreté. [SDK OpenRGB](https://openrgb.org/sdk.html), [avertissement du projet](https://raw.githubusercontent.com/CalcProgrammer1/OpenRGB/master/README.md), [compatibilités](https://openrgb.org/devices.html)

**Décision de faisabilité :** feu vert pour l’application et le moteur d’effets ; validation conditionnelle pour chaque clavier ; décision suspendue pour le contrôle individuel des ventilateurs tant que la chaîne matérielle n’est pas identifiée.

## 6. Spécification du dégradé lent

L’effet doit combiner un dégradé spatial — plusieurs couleurs simultanées sur des LED différentes — et une évolution temporelle très lente. Un simple changement uniforme de couleur ne satisfait pas le besoin principal.

Paramètres proposés :

- Palette de 2 à 8 couleurs, avec interpolation continue et boucle sans rupture.
- Durée d’un cycle : 30 secondes à 60 minutes ; valeur initiale de 10 minutes.
- Luminosité de 0 à 100 %, avec premier essai à 20 %.
- Sens de circulation, décalage de départ et étendue du dégradé.
- Organisation par anneau de ventilateur, par canal, ou sur une chaîne virtuelle continue lorsque le matériel le permet.
- Inversion de l’ordre des LED et décalage entre ventilateurs indépendants.

Le moteur calcule chaque couleur à partir de la position de la LED et du temps écoulé. La vitesse de l’animation est indépendante de la fréquence d’envoi : une boucle de dix minutes peut être rafraîchie 20 fois par seconde pour rester fluide. Cette fréquence initiale sera plafonnée selon les limites documentées du périphérique ; les trames périmées seront abandonnées plutôt qu’accumulées.

L’horloge sera monotone. Au retour de veille, reprendre proprement la phase calculée, sans rejouer les trames manquées. Une interpolation perceptuelle et une conversion adaptée aux sorties RGB seront évaluées dans le simulateur. La quantification et le rendu des LED peuvent limiter la douceur des transitions, surtout à faible luminosité.

## 7. Comportement de l’application

### Parcours principal

1. Ouvrir l’app en simulation, consulter les appareils connus et leurs capacités.
2. Associer un appareil à un connecteur validé ; afficher explicitement « par LED », « par zone » ou « effets intégrés ».
3. Prévisualiser un profil, puis l’appliquer uniquement à la cible sélectionnée.
4. Enregistrer le profil et activer, si souhaité, le lancement automatique.

L’écran principal présente les appareils, leur disponibilité, le profil actif et la luminosité. L’éditeur propose une vue du clavier ou des anneaux de LED. Un appareil incompatible reste visible avec une raison précise ; aucune fonction individuelle ne doit être simulée dans l’interface comme si elle était matériellement disponible.

### Démarrage et incidents

- Démarrage à l’ouverture de session utilisateur, sans élévation permanente. L’effet avant connexion Windows n’est pas couvert.
- Attente des services nécessaires et reconnexions espacées, sans bloquer l’ouverture de session.
- Fermeture de la fenêtre vers la zone de notification ; commande « Quitter » distincte.
- Un seul connecteur propriétaire du contrôle d’un appareil ; pas de concurrence volontaire avec l’outil constructeur.
- En cas de déconnexion ou d’erreur répétée, suspendre les écritures sur la cible et informer l’utilisateur.
- Commande « Libérer le contrôle » distincte d’« Éteindre les LED » : la première arrête l’intervention de l’app, la seconde envoie une couleur noire.
- À la sortie normale, libérer via l’API officielle si disponible. Le retour exact au profil précédent dépend du constructeur et ne peut être promis après un crash ; le matériel peut garder la dernière couleur ou revenir à son effet autonome.
- Après un crash répété, proposer un redémarrage en simulation sans connexion matérielle.

## 8. Architecture proposée

Application Windows native en C#/.NET avec WPF, choix provisoire adapté à une première version Windows. Choisir une version maintenue au démarrage de l’implémentation. Aucun service système propre au projet n’est nécessaire pour le MVP.

Composants :

- Interface et profils : configuration locale versionnée, écriture atomique et copie de secours.
- Moteur d’effets : génération de tableaux de couleurs indépendante des marques.
- Registre des capacités : identifiant stable, zones, géométrie, nombre de LED, limites et mode de contrôle.
- Routeur : liste d’appareils autorisés, exclusion Corsair, cadence et gestion des conflits.
- Connecteurs : simulateur, LampArray, Razer, puis ASUS et contrôleur du boîtier après validation.
- Journal local : connexions, erreurs, décisions de routage ; aucune collecte de frappes clavier ni télémétrie obligatoire.

Chaque connecteur doit pouvoir annoncer ses capacités, acquérir le contrôle, appliquer une trame à une cible et libérer le contrôle. Les interfaces trop globales pour respecter les exclusions ne seront pas activées.

Un éventuel backend OpenRGB devra être configuré avant sa première exécution pour éviter les détections non nécessaires et l’accès à Corsair ; filtrer les appareils uniquement dans notre interface serait insuffisant. Si cette isolation ne peut pas être établie, le backend est refusé pour ce PC. Son serveur éventuel doit rester accessible uniquement en boucle locale.

La phase bureau ajoutera des connecteurs réseau séparés. Les protocoles, l’authentification et les limites de fréquence seront étudiés par appareil ; une synchronisation à la même cadence que les LED du PC ne sera pas supposée.

## 9. Risques et protections

Les niveaux ci-dessous sont une appréciation de conception, pas une probabilité de panne mesurée.

| Risque | Conséquence | Protection et condition d’arrêt |
| --- | --- | --- |
| Protocole matériel incorrect ou expérimental | Blocage d’un contrôleur, dommage possible | API documentées en priorité ; aucune exploration SMBus/I²C, aucun pilote bas niveau ajouté pour contourner une incompatibilité |
| Confusion ARGB 5 V / RGB 12 V, surcharge ou mauvais câblage | Dommage électrique | Aucun recâblage dans le MVP ; toute évolution exige les notices, limites de courant et un montage hors tension |
| Commandes globales ou détection trop large | Prise de contrôle de Corsair, conflit avec iCUE | Exclusions avant initialisation du backend ; refus des connecteurs incapables d’isoler les cibles |
| Deux applications contrôlent le même appareil | Scintillement, couleurs instables, accès refusé | Attribution explicite du contrôle, respect des priorités Windows et arrêt en cas de conflit |
| Contrôleur partagé avec une fonction de refroidissement | Régression sur pompe ou ventilation | Exclure le contrôleur tant que la séparation des fonctions n’est pas démontrée ; aucune commande thermique |
| Écritures persistantes répétées | Usure de mémoire ou configuration altérée | Animation par commandes volatiles ; aucun enregistrement matériel à chaque trame, aucun flash |
| Cadence excessive ou SDK bloqué | Charge CPU, saccades, instabilité du service | Limites par appareil, délais d’attente, aucune file illimitée et arrêt après erreurs répétées |
| Mise à jour constructeur | Intégration cassée | Versions testées consignées, connecteur désactivable, revalidation après mise à jour |
| Topologie ou précision colorimétrique insuffisante | Résultat inférieur au rendu iCUE souhaité | Test de couleurs indépendantes et de fluidité avant de valider le boîtier |

Les API officielles réduisent l’exposition aux protocoles inconnus, sans supprimer les défauts éventuels des pilotes, firmwares ou SDK. Une luminosité initiale réduite facilite les essais, mais ne protège pas d’un mauvais branchement ni d’un protocole erroné.

## 10. Plan de réalisation et critères de validation

### Étape A — Qualification sans commande RGB

Compléter l’inventaire, tracer la chaîne contrôleur → ports → ventilateurs, consulter les notices et identifier les dépendances logicielles. Produire une fiche par appareil : référence, firmware connu, API, capacités, portée des commandes, limites et retour au gestionnaire précédent.

**Sortie :** une méthode de contrôle acceptable est documentée pour chaque cible. Une cible non qualifiée reste désactivée.

### Étape B — Simulateur et moteur

Implémenter les profils et les vues virtuelles du clavier et des ventilateurs. Tester les transitions, le bouclage, la vitesse indépendante de la cadence, les limites d’indices et les erreurs de configuration.

**Sortie :** un dégradé de dix minutes est visible en simulation ; deux positions distinctes peuvent recevoir des couleurs différentes ; aucune connexion matérielle n’est nécessaire.

### Étape C — Un clavier

Sur un modèle qualifié, tester une couleur fixe à faible luminosité, deux touches de couleurs différentes puis l’animation. Vérifier libération, déconnexion, veille et coexistence avec iCUE.

**Sortie :** mapping correct, contrôle stable et aucun effet observé sur Corsair. La vérification de non-régression combine observation et inspection des commandes routées.

### Étape D — Un canal d’éclairage du boîtier

Après qualification, envoyer une trame connue avec deux LED de couleurs différentes, puis tester le dégradé. Vérifier les répétitions dues au hub et les limites avant d’ajouter les autres canaux. Aucun changement de vitesse des ventilateurs ou de pompe n’est admis.

**Sortie :** contrôle individuel démontré sur les LED réellement indépendantes, rendu lent satisfaisant et absence d’erreurs pendant une session de deux heures. Ce test vérifie la stabilité observée, pas une garantie absolue de sûreté.

### Étape E — Usage quotidien

Activer l’ouverture de session automatique après les essais manuels. Vérifier cinq démarrages de session, cinq cycles de veille/reprise et le fonctionnement lorsque le clavier ou un service est absent.

**Critères finaux :**

- Le profil reprend sans intervention une fois ses dépendances disponibles.
- L’app reste réactive et permet de suspendre les envois immédiatement.
- Corsair demeure géré par iCUE ; aucun accès Corsair par nos connecteurs.
- Le rendu tient la cadence autorisée sans accumulation de trames.
- Objectif indicatif pour notre processus, fenêtre fermée : moins de 1 % CPU moyen et 200 Mo de mémoire sur le PC cible ; dépendances constructeur mesurées séparément.
- La désactivation du démarrage et la désinstallation rendent le contrôle aux outils habituels, sans modifier les réglages de refroidissement.

## 11. Charge indicative et points de décision

Estimation de cadrage pour une personne expérimentée, à réviser après inventaire :

| Lot | Ordre de grandeur |
| --- | --- |
| Inventaire et qualification documentaire | 0,5 à 2 jours, hors attente de références |
| Simulateur, moteur et profils | 2 à 4 jours |
| Premier clavier via API exploitable | 1 à 3 jours |
| Contrôleur du boîtier déjà documenté et compatible | 2 à 5 jours |
| Démarrage, packaging et essais de coexistence | 2 à 4 jours |

Prévoir environ deux à quatre semaines pour un MVP utilisable si les interfaces conviennent. Un SDK indisponible ou un contrôleur opaque peut invalider cette estimation et conduire à exclure l’appareil. L’achat d’un contrôleur ne sera envisagé qu’après vérification du câblage, de l’API et des limites électriques ; aucun achat n’est nécessaire pour rédiger ou développer le simulateur.

Les décisions encore ouvertes concernent les références matérielles, le niveau d’indépendance attendu entre ventilateurs et l’acceptation éventuelle d’un backend tiers. La priorité reste la conservation du fonctionnement actuel de Corsair et du refroidissement.

## 12. Références et portée de l’étude

Sources primaires consultées le 12 novembre 2026 : Microsoft Dynamic Lighting, SDK Razer Chroma, guide ASUS Aura, documentation Cooler Master, fiche technique et manuel MSI MAG X870 TOMAHAWK WIFI, présentation MSI Mystic Light et projet officiel OpenRGB, liés dans les sections correspondantes. Les documentations de SDK peuvent décrire des générations anciennes : elles établissent des possibilités techniques, pas une certification de la configuration actuelle.

Cette livraison est une spécification documentaire. Aucun logiciel, pilote, service de démarrage ou profil RGB n’a été installé ou modifié.
