[🔙 Retour au sommaire](index.md)

# ❓ FAQ

## Jeedom

### Comment fonctionne l'historique de Jeedom ?
→ Vos appareils renvoie des états et des informations que vous pouvez historiser = garder une trace dans la base de données pour ensuite consulter les graphiques ou retrouver des informations à comparer avec des plus récentes.  

Par exemple : une prise connectée indique le voltage actuel toutes les 20 secondes (220.15V, 224.2V, 222.35V etc). 
- Ce chiffre est stocké dans la table `history` de la base de données qui contient toutes les données reçues pour la journée en cours. Avec un retour toutes les 20 secondes, il y aura en fin de journée, après 24h, **4320** enregistrements pour le voltage de cette prise (sauf si vous avez configuré "Limiter à une valeur toute les" dans la commande).

- Chaque jour (par défaut à 2h00 : cron archive dans le moteur de tâches) Jeedom fait un `archivage` (ou *lissage de la nuit*) de cette table, c'est à dire qu'il transfère l'ensemble des données vers une seconde table : `historyArch` en appliquant un lissage si vous l'avez configuré (moyenne, minimum etc), qui ne garde alors que 1 seul enregistrement par heure, ce qui réduit à 24 au lieu de 4320 les enregistrements pour la commande voltage de la prise.  
Si vous n'avez rien configuré les 4320 sont transférés tel quels.

- 💡 L'archivage transfère **toutes** les données de la table history (les dernières 24h) comme il a lieu à 2h du matin ça inclue aussi les dernières données entre minuit et 2h00 sauf si vous avez réglé le délai avant archivage sur 2h (fortement conseillé dans réglages/système/configuration/équipements : Délai avant archivage) auquel cas il prendra bien tout de 0h00 à 23h59.

- Ensuite, en général à 05h25, Jeedom réalise une `sauvegarde` (cron backup dans le moteur de tâches) de tout votre Jeedom y compris la base de données, d'où l'importance de l'avoir traitée et réduite en taille avant cette heure là.

---

### Quelle est la configuration idéale de Jeedom pour utiliser ce plugin ?
Le mieux est de garder l'archivage à 02h00 avec un délai avant archivage de 2h. D'avoir la sauvegarde qui se lance à 05h25. À partir de là réglez les lissages pour que le jour se lance à 01h00 (avant l'archivage donc), la semaine à 03h00, le mois à 04h00 (avant la sauvegarde).  Pour l'année cela n'a pas trop d'importance, ça peut être 05h00 ou n'importe quelle autre heure disponible.  
Eviter les configurations bizarres comme par exemple un délai d'archivage de 24h...

---

### Quel est l'intérêt d'utiliser HistoLisse dans Jeedom ?
Avec plus de 600 heures de développement il faut espérer qu'il y ait un véritable intérêt !  
La problématique, comme expliqué plus haut, est que Jeedom ne propose que : de ne faire aucun lissage / ou bien de ne conserver qu'un seul enregistrement par heure. Dans le premier cas on a très vite une base de données énorme et dans le 2nd cas on peut manquer d'informations utiles à long terme.

Pour beaucoup de commandes 1 enregistrement par heure est suffisant. Mais ça peut être frustrant quand il s'agit de commande qu'on veut comparer d'un mois ou année sur l'autre. Essentiellement toutes les commandes de consommation de courant ou de production solaire dont on veut garder un historique précis mais léger, toutes les 15 Min ou 30 Min pendant plusieurs mois. HistoLisse permet cela.  
A l'inverse il permet aussi de ne garder qu'un enregistrement toutes les 6 heures, par exemple, sur du long terme soit 4 par jour au lieu de 24.

L'autre intérêt est pour les commandes du genre Zigbee (sur courant) ou Téléinfo qui ont tendance à envoyer des données toutes les 2 à 8 secondes ce qui surcharge très vite les tables et ralentit d'autant l'affichage des graphiques.  
En traitant chaque heure ces commandes avec un intervalle à la minute on réduit énormément la taille de la table history et donc le temps de chargement des graphiques.

Parfois sur ces commandes on a besoin de cette haute fréquence d'enregistrements pour faire des calculs en direct comme par exemple du délestage ou des choses comme ça. HistoLisse permet cela aussi via l'âge des données : On peut ne pas traiter la dernière minute ou les 10 dernières minutes etc d'une commande et donc à la fois réduire le nombre d'enregistrements de la journée tout en gardant une information précise en live.

---

## Les lissages

### Pourquoi les lissages ont lieu à hh:01 et pas à hh:00 ?
A l'heure pile Jeedom effectue tout un tas de tâches et de crons. Votre matériel est alors très sollicité en mémoire et processeur.  
Même si les lissages ne durent que quelques secondes, il est plus efficace de les décaler d'une minute pour ne pas surcharger votre matériel.  
Pour autant même s'il s'exécute à hh:01, le lissage Heure traite bien les données de l'heure précédente entre hh:00 et hh:59 inclus.

---

### Comment marchent les périodes de lissage ?
Les lissages fonctionnent avec leur période de traitement correspondante : d'une heure, un jour, une semaine, un mois, une année à la fois.  
On ne peut donc pas traiter par exemple 3 semaines d'un coup ou alors il faudrait ensuite attendre 3 semaines avant de retraiter la commande, ce serait trop complexe à gérer avec des traitements en doublon.

---

### Le lissage année a-t-il un intérêt réel au quotidien ?
Généralement non, mais il est utile dans certains cas précis. Vous aurez sans doute déjà un lissage par mois qui fera l'essentiel du travail de réduction des informations. De plus beaucoup de commandes ont une purge inférieure ou égale à 1 an et donc aucune raison de lisser par année. Par contre il peut vous permettre de traiter les quelques commandes avec une purge supérieure à 1 an ou sans purge.  

Quand l'utiliser ?
Pour les données anciennes (plus d’1 an) non traitées par les lissages mensuels.  
Pour les commandes sans purge ou avec une purge > 1 an.  
Lors de la première installation du plugin, pour optimiser les données archivées depuis longtemps.

En effet, même s'il est prévu pour se lancer une fois par an, vous pouvez tout à fait le lancer plusieurs fois en changeant son heure/jour/mois d'exécution dans **Réglage des lissages** pour traiter par plage d'un an vos anciennes données.  
Exemple :  
Vous avez une commande *85 Téléinfo-indexHP* lissée en mode maximum par Jeedom mais sans purge avec 4 ans de données dans la table historyArch (admettons qu'on a 4 années complètes de données dont 6 mois sur l'année en cours = 1 valeur/heure → ~35 000 points ).  
Objectif : Réduire le nombre de points tout en conservant l’essentiel.

- On est samedi 19 juillet 2025, il est 10h30 et vous venez juste d'installer le plugin.

Étape 1 à 10h30
- Dans le réglage des lissages vous enregistrez pour Année : **11h00, jour 19, mois 7** donc aujourd'hui à la prochaine heure.
- Dans le réglage de la commande **85** vous activez le lissage Année en **mode maximum** (pour garder l'index le plus élevé par intervalle) avec un **arrondi à 0** (les index n'ont pas de décimale) et un **intervalle à 360min** (pour ne garder qu'une valeur toutes les 6 heures, c'est suffisant pour un index de 3 ans) et vous indiquez pour le **Jour Fin -1461** (1461 jours en arrière = 4 ans : valeur maximale autorisée) le jour début va se règler sur -1826 (5 ans) et les dates vous indiquent un traitement du 19 juillet 2020 0h00 au 19 juillet 2021 23h59.
- A 11h01 ce premier lissage par année est fait pour la commande 85 (et autres si configurées) vous pouvez vérifier dans Jeedom (graphique ou historique) que vous n'avez plus qu'une valeur toutes les 6 heures pour les enregistrements **avant** le 19 juillet 2021 23h59.

Étape 2 à 11h02
- Dans le réglage des lissages vous changez l'heure pour Année à 12h00.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -1095** (3 ans) et les dates vous indiquent désormais un traitement du 20 juillet 2021 0h00 au 20 juillet 2022 23h59.
- A 12h01 ce second lissage par année est fait.

Étape 3 à 12h02
- Dans le réglage des lissages vous changez l'heure pour Année à 13h00.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -729** (2 ans) et les dates vous indiquent désormais un traitement du 21 juillet 2022 0h00 au 21 juillet 2023 23h59.
- A 13h01 ce 3ème lissage est fait.

Étape 4 à 13h02
- Dans le réglage des lissages vous changez l'heure pour Année à 14h00.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -363** (2 ans) et les dates vous indiquent désormais un traitement du 22 juillet 2023 0h00 au 21 juillet 2024 23h59.
- A 14h01 ce 4ème lissage est fait.

Étape 5 à 14h02
- Dans le réglage des lissages vous changez l'heure pour Année à 15h00.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -200** (pour finir au 31/12/24) et les dates vous indiquent désormais un traitement du 1 janvier 2024 0h00 au 31 décembre 2024 23h59. (NB: Les enregistrements entre 1 janvier 2024 et le 21 juillet 2024 seront vérifiés en doublon de l'étape 4 mais ce n'est pas un problème.)
- A 15h01 ce 5ème lissage est fait.

Étape 6 à 15h02
Voilà ! Vous avez nettoyé votre commande 85 (et d'autres) sur la période du 19 juillet 2020 au 31 décembre 2024. Les ~35 000 enregistrements sur 4 ans (dont 6 mois sur l'année en cours) sont devenus 9424 (73% de réduction) répartis en : 5104 (1 toutes les 6h) sur 3,5 ans et 4320 (1 par heure) pour les 6 mois de l'année en cours non traitée. Ces lissages ont divisé par 4 le nombre d'enregistrements sans perte des informations utiles.
- Dans le réglage des lissages vous choisissez maintenant votre vraie date pour le lissage Année : **05h00, jour 1, mois 1** soit un prochain lissage le 1er janvier 2026 à 5h du matin.
- Dans le réglage de la commande **85** vous changez le lissage Année en indiquant pour le **Jour Fin -1** et les dates vous indiquent désormais un traitement (qui aura lieu le 1er janvier 2026) du 31 décembre 2024 0h00 au 31 décembre 2025 23h59.

---

### J'ai eu une panne de mon Jeedom pendant 2h ce jour, puis-je rattrapper les lissages Heure non faits ?
Il n'est pas possible de rattraper ce qui n'a pas été fait. C'est pourquoi il est très important de configurer des lissages en cascade en ajoutant par exemple un lissage par jour en plus du lissage par heure et éventuellement un lissage par semaine même si c'est avec les mêmes paramètres (mode, arrondi, intervalle) afin d'être sûr que l'information soit au moins traitée une fois.

---

## Divers

### Où sont stockées les données ?
→ Dans le dossier `data` du plugin, via des fichiers json qu'il est fortement conseillé de ne pas modifier !

[🔙 Retour au sommaire](index.md)
