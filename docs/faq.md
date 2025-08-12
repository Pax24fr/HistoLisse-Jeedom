[🔙 Retour au sommaire](index.md)

# ❓ FAQ

## Jeedom

### Comment fonctionne l'historique de Jeedom ?
→ Vos appareils renvoie des états et des informations que vous pouvez historiser = garder une trace dans la base de données pour ensuite consulter les graphiques ou retrouver des informations à comparer avec des plus récentes.  

Par exemple : une prise connectée indique le voltage actuel toutes les 20 secondes (220.15V, 224.2V, 222.35V etc). 
- Ce chiffre est stocké dans la table `history` de la base de données qui contient toutes les données reçues pour la journée en cours. Avec un retour toutes les 20 secondes, il y aura en fin de journée, après 24h, **4320** enregistrements pour le voltage de cette prise (sauf si vous avez configuré "Limiter à une valeur toute les" dans la commande).

- Chaque jour (par défaut à 2h00 : cron archive dans le moteur de tâches) Jeedom fait un `archivage` (ou *lissage de la nuit*) de cette table, c'est à dire qu'il transfère l'ensemble des données vers une seconde table : `historyArch` en appliquant un lissage si vous l'avez configuré (moyenne, minimum etc), qui ne garde alors que 1 seul enregistrement par heure, ce qui réduit à 24 au lieu de 4320 les enregistrements pour la commande voltage de la prise.  
Si vous n'avez rien configuré les 4320 sont transférés tel quels.

💡 *L'archivage transfère **toutes** les données de la table history (les dernières 24h) comme il a lieu à 2h du matin ça inclue aussi les dernières données entre minuit et 2h00 sauf si vous avez réglé le délai avant archivage sur 2h (fortement conseillé dans réglages/système/configuration/équipements : Délai avant archivage) auquel cas il prendra bien tout de 0h00 à 23h59.*

- Ensuite, en général à 05h25, Jeedom réalise une `sauvegarde` (cron backup dans le moteur de tâches) de tout votre Jeedom y compris la base de données, d'où l'importance de l'avoir traitée et réduite en taille avant cette heure là.

---

### Quelle est la configuration idéale de Jeedom pour utiliser ce plugin ?
Le mieux est de garder l'archivage à 02h00 mais avec un délai avant archivage de 2h et d'avoir la sauvegarde qui se lance à 05h25.  
Éviter les configurations bizarres comme par exemple un délai d'archivage de 24h...

---

### Quelle devrait être la taille de ma base de données ?
Difficile de répondre à ça puisque ça dépend du nombre d'appareils que vous avez et du nombre de commandes historisées ainsi que de l'ancienneté de votre Jeedom.  
Disons qu'en général, pour 70 équipements et 200 commandes historisées, une base Jeedom fera entre 30 et 60 Mo avec la table history  autour de 5 à 10 Mo et la table historyArch entre 20 et 50 Mo.  
En tout état de cause si votre base de données dépasse les 200 Mo vous avez un sérieux problème de nettoyage à faire !

---

### Quel est l'intérêt d'utiliser HistoLisse dans Jeedom ?
Avec plus de 700 heures de développement il faut espérer que ce plugin ait un véritable intérêt !  
La problématique, comme expliqué plus haut, est que Jeedom ne propose que : de ne faire aucun lissage / ou bien de ne conserver qu'un seul enregistrement par heure. Dans le premier cas on a très vite une base de données énorme et dans le 2nd cas on peut manquer d'informations utiles à long terme.

Pour beaucoup de commandes 1 enregistrement par heure est suffisant, c'est même trop à long terme. Mais ça peut être frustrant quand il s'agit de commande qu'on veut comparer d'un mois ou d'une année sur l'autre. Essentiellement toutes les commandes de consommation de courant ou de production solaire dont on veut garder un historique précis mais léger, toutes les 15 Min ou 30 Min pendant plusieurs mois. HistoLisse permet cela.  
A l'inverse, 1 par heure peut-être trop à long terme, HistoLisse permet aussi, par exemple, de ne garder qu'un enregistrement toutes les 6 heures sur du long terme (+ de 3 mois) soit 4 par jour au lieu de 24.

L'autre intérêt est pour les commandes du genre Zigbee (sur courant) ou Téléinfo qui ont tendance à envoyer des données toutes les 1 à 10 secondes ce qui surcharge très vite les tables et ralentit d'autant l'affichage des graphiques.  
En traitant à chaque heure ces commandes avec un intervalle à la minute on réduit énormément la taille de la table history et donc le temps de chargement des graphiques tout en conservant la possibilité ensuite de l'archiver par 15 ou 30min.

Parfois sur ces commandes on a besoin de cette haute fréquence d'enregistrements pour faire des calculs en direct comme par exemple du délestage, des moyennes sur 1 ou 10min ou des choses comme ça.  
HistoLisse permet cela aussi via l'âge des données : On peut ne pas traiter la dernière minute (options jusqu'à 2 dernières heures)d'une commande et donc à la fois réduire le nombre d'enregistrements de la journée tout en gardant une information très précise en live.

---

## Les lissages

### Comment marchent les périodes de lissage ?
Les lissages fonctionnent avec leur période de traitement correspondante : d'une heure, un jour, une semaine, un mois, une année à la fois.  
On ne peut donc pas traiter par exemple 3 semaines d'un coup ou alors il faudrait ensuite attendre 3 semaines avant de retraiter la commande, ce serait trop complexe à gérer avec des traitements en doublon.

------

### A quelle heure programmer mes lissages ?
Tout dépend de votre configuration Jeedom mais si elle correspond à la configuration idéale donnée plus haut alors :
- réglez les lissages pour que le Jour se lance à 01h (avant l'archivage), le Semaine à 03h, le Mois à 04h (avant la sauvegarde). Pour l'année, peu d'importance, ça peut être 05h ou n'importe quelle autre heure non utilisée.  
- lissage par Mois le plus simple pour le Jour du mois d'exécution c'est le 1er du mois. Si malgré tout vous choisissez le 29, 30, 31 il sera executé le dernier jour du mois si moins de jours que la date indiquée.
- lissage par Année le plus simple est bien sûr de choisir le 1/1 afin de traiter une année complête.
Règle générale : choisissez des heures différentes pour chaque lissage et qui ne soit pas celles de l'archivage ni de la sauvegarde Jeedom.  
Pour le Jour, si vous uilisez l'âge des données, il n'en tiendra pas compte, donc il faut le programmer au moins 1h après l'heure de la dernière donnée qui sera traitée par l'archivage (si configuration idéale = Jour à 1h).

---

### Pourquoi les lissages ont lieu à hh:01 et pas à hh:00 ?
A l'heure pile Jeedom effectue tout un tas de tâches et de crons. Votre matériel est alors très sollicité en mémoire et processeur.  
Même si les lissages ne durent que quelques secondes, il est plus efficace de les décaler d'une minute pour ne pas surcharger votre matériel.  
Pour autant même s'il s'exécute à hh:01, le lissage Heure traite bien les données de l'heure précédente entre hh:00 et hh:59 inclus.

---

### Le lissage par Année a-t-il un intérêt réel ?
Généralement non. Vous aurez sans doute déjà un lissage par mois qui fera l'essentiel du travail de réduction des informations. De plus beaucoup de commandes ont une purge inférieure ou égale à 1 an et donc aucune raison de lisser par année. Par contre il peut vous permettre de traiter les quelques commandes avec une purge supérieure à 1 an ou sans purge.  

Quand l'utiliser ?  
Pour les commandes sans purge ou avec une purge > 1 an.  
Lors de la première installation du plugin, pour lisser les données archivées depuis longtemps.

En effet, même s'il est prévu pour se lancer une fois par an, vous pouvez tout à fait le lancer plusieurs fois en changeant son heure/jour/mois d'exécution dans **Réglage des lissages** pour traiter par plages d'1 an à la fois vos anciennes données.

**Exemple :**  
Vous avez une commande *85 Téléinfo-indexHP* lissée en mode **maximum** par Jeedom mais __sans purge__ avec 4 ans de données dans la table historyArch (admettons qu'on a 4 années complètes de données dont 6 mois sur l'année en cours = 1 valeur/heure → ~35 000 points ).  
Objectif : Réduire le nombre de points tout en conservant l’essentiel de l'information.

- Vous venez juste d'installer le plugin.

1.  
- Via la **Gestion des commandes** vous ajoutez la commande 85 à HistoLisse.
- Dans le réglage des lissages vous enregistrez pour Année : **06h00, jour 1, mois 1** donc exécution le 1er janvier de chaque année à 6h.
- Dans le réglage de la commande **85** vous activez (de préférence uniquement) le lissage Année en **mode maximum** (pour garder l'index le plus élevé par intervalle) avec un **arrondi à 0** (les index n'ont pas de décimale) et un **intervalle à 360min** (pour ne garder qu'une valeur toutes les 6 heures, c'est suffisant pour un index après 1 an) et vous indiquez pour le **Jour Fin -1461** (1461 jours en arrière = 4 ans : valeur maximale autorisée) le jour début va se règler sur -1826 (5 ans) et vous sauvegardez.
- Vous cliquez sur le bouton "Lancer ces lissages immédiatement". Ce premier lissage par année est fait pour la commande 85 vous pouvez vérifier dans Jeedom (graphique ou historique) que vous n'avez plus qu'une valeur toutes les 6 heures pour les enregistrements **entre 5 et 4 ans avant** aujourd'hui.

2.  
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -1095** (3 ans).
- Vous cliquez sur le bouton "Lancer ces lissages immédiatement". Ce second lissage par année est fait pour la commande 85 pour les enregistrements **entre 4 et 3 ans avant** aujourd'hui.

3.  
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -729** (2 ans).
- Vous cliquez sur le bouton "Lancer ces lissages immédiatement". Ce 3ème lissage par année est fait pour la commande 85 pour les enregistrements **entre 3 et 2 ans avant** aujourd'hui.

4.  
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -363** (1 ans).
- Vous cliquez sur le bouton "Lancer ces lissages immédiatement". Ce 4ème lissage par année est fait pour la commande 85 pour les enregistrements **entre 2 et 1 an avant** aujourd'hui.

5.  
- Vérifiez le numéro du jour d'aujourd'hui, par exemple si nous sommes le 1er juillet 2025 c'est le 182ème jour de l'année.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -182** (pour finir au 31/12/24). *(NB: Les enregistrements entre 1 janvier 2024 et le 1 juillet 2024 seront vérifiés en doublon de l'étape 4 mais ce n'est pas un problème.)*
- Vous cliquez sur le bouton "Lancer ces lissages immédiatement". Ce 5ème lissage par année est fait pour la commande 85 pour les enregistrements jusqu'au 31 décembre dernier.

6.  
Voilà ! Vous avez nettoyé votre commande 85 sur la période du 1er juillet 2020 au 31 décembre 2024. Les ~35 000 enregistrements sur 4 ans (dont 6 mois sur l'année en cours) sont devenus 9424 (73% de réduction) répartis en : 5104 (1 toutes les 6h) sur 3,5 ans et 4320 (1 par heure) pour les 6 mois de l'année en cours non traitée. Ces lissages ont divisé par 4 le nombre d'enregistrements sans perte des informations utiles.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -1** et les dates vous indiquent désormais un traitement (qui aura lieu le 1er janvier 2026) du 31 décembre 2024 0h00 au 31 décembre 2025 23h59.

Si vous voulez traiter plusieurs commandes à la fois, alors commencez par régler le lissage année pour chaque commande puis aller dans **Réglage des lissages** et cliquez sur le bouton "Lancer immédiatement" en face de Année. Le lissage par année se lancera pour toutes les commandes réglées.

---

### Je ne vois pas certains lissages pour une commande
Les lissages ne sont visibles et activables que si le délai de purge de cette commande est supérieur à la période du lissage.  
Par exemple si le délai avant purge = 7 jours → les lissages Semaine et suivants ne seront pas visibles.

---

### Combien de temps dure un lissage ?
Un certain temps... Difficile de donner une règle, tout dépend de votre matériel, de l'occupation de Jeedom à ce moment là etc...  
Sur un "vieux" RPI 3b+ avec 2Go de mémoire, il faut compter un peu moins de 3 secondes pour traiter 10 000 enregistrements réparties sur une vingtaine de commandes.  Un RPI 4 avec 8Go peut traiter 100 000 enregistrements à la seconde.
En général, le lissage de chaque heure va durer entre 1 et 2 dixièmes de secondes par commande à traiter suivant son volume de données (0 à 1000). Un lissage Mois qui traite 100 000 lignes dure moins de 20 secondes.  

---

### J'ai eu une panne de mon Jeedom pendant 2h ce jour, puis-je rattrapper les lissages Heure non faits ?
Il n'est pas possible de rattraper ce qui n'a pas été fait. C'est pourquoi il est très important de configurer des lissages en cascade en ajoutant par exemple un lissage par jour en plus du lissage par heure et éventuellement un lissage par semaine même si c'est avec les *mêmes paramètres* (mode, arrondi, intervalle) afin d'être sûr que l'information soit au moins traitée une fois en cas de panne, mieux vaut rester attentif quand vous modifez les réglages de la commande !
En dehors du lissage heure, si vous vous rendez compte rapidement qu'un lissage n'a pas eu lieu, exemple celui du jour si on est toujours le lendemain, vous pouvez aller dans **Réglage des lissages** et cliquez sur le bouton "Lancer immédiatement" en face de Jour. Le lissage par jour se lancera pour toutes les commandes réglées de la veille.

---

## Divers

### Est-ce que je risque de perdre des données ?
Il n'y a aucune raison de perdre des données de votre historique en dehors d'une mauvaise configuration de votre part dans le réglage de commande, par exemple vous avez saisi par erreur un intervalle de 600 au lieu de 60 minutes et donc vous n'avez plus qu'un point toutes les 10h au lieu d'un par heure.  
Si vous vous en rendez compte rapidement, il reste la sauvegarde quotidienne de Jeedom pour restaurer votre base de données (en ne restaurant que les valeurs manquantes mais c'est assez technique).

### Où sont stockées les données ?
→ Dans le dossier `data` du plugin, via des fichiers json qu'il est fortement conseillé de **ne pas modifier** !  
En cas de "problème", ils sont aussi dans la sauvegarde quotidienne de Jeedom, dans le répertoire data du plugin.

[🔙 Retour au sommaire](index.md)
