[🔙 Retour au sommaire](index.md)

# ❓ FAQ

## Comment fonctionne l'historique de Jeedom ?
→ Vos appareils renvoie des états et des informations que vous pouvez historiser = garder une trace dans la base de données pour ensuite consulter les graphiques ou retrouver des informations à comparer avec des plus récentes.  

Par exemple : une prise connectée indique le voltage actuel toutes les 20 secondes (220.15V, 224.2V, 222.35V etc). 
- Ce chiffre est stocké dans la table `history` de la base de données qui contient toutes les données reçues pour la journée en cours. Avec un retour toutes les 20 secondes, il y aura en fin de journée, après 24h, **4320** enregistrements pour le voltage de cette prise (sauf si vous avez configuré "Limiter à une valeur toute les" dans la commande).

- Chaque jour (par défaut à 2h00 : cron archive dans le moteur de tâches) Jeedom fait un `archivage` (ou *lissage de la nuit*) de cette table, c'est à dire qu'il transfère l'ensemble des données vers une seconde table : `historyArch` en appliquant un lissage si vous l'avez configuré (moyenne, minimum etc), qui ne garde alors que 1 seul enregistrement par heure, ce qui réduit à 24 au lieu de 4320 les enregistrements pour la commande voltage de la prise.  
Si vous n'avez rien configuré les 4320 sont transférés tel quels.

- 💡 L'archivage transfère **toutes** les données de la table history (les dernières 24h) comme il a lieu à 2h du matin ça inclue aussi les dernières données entre minuit et 2h00 sauf si vous avez réglé le délai d'archivage sur 2h (fortement conseillé dans réglages/système/configuration/équipements : Délai avant archivage) auquel cas il prendra bien tout de 0h00 à 23h59.

- Ensuite, en général à 05h25, Jeedom réalise une `sauvegarde` (cron backup dans le moteur de tâches) de tout votre Jeedom y compris la base de données, d'où l'importance de l'avoir traitée et réduite en taille avant cette heure là.

## Pourquoi les lissages ont lieu à hh:01 et pas à hh:00 ?
A l'heure pile Jeedom effectue tout un tas de tâches et de crons. Votre matériel est alors très sollicité en mémoire et processeur.  
Même si les lissages ne durent que quelques secondes, il est plus efficace de le décaler d'une minute pour ne pas surcharger votre matériel.  
Pour autant même s'il s'exécute à hh:01, le lissage Heure traite bien les données de l'heure précédente entre hh:00 et hh:59 inclus.

## Comment marchent les périodes de lissage ?
Les lissages fonctionnent avec leur période de traitement correspondante : d'une heure, un jour, une semaine, un mois, une année à la fois.  
On ne peut donc pas traiter par exemple 3 semaines d'un coup ou alors il faudrait ensuite attendre 3 semaines avant de retraiter la commande, ce serait trop complexe à gérer avec des traitements en doublon.

## Le lissage année a-t-il un intérêt réel ?
Non en général il ne vous servira pas. Vous aurez sans doute déjà un lissage par mois qui fera l'essentiel du travail de réduction des informations. De plus beaucoup de commandes ont une purge inférieure ou égale à 1 an et donc aucune raison de lisser par année.  
Par contre il peut vous permettre de traiter les quelques commandes avec une purge supérieure à 1 an ou sans purge.  
Même s'il est prévu pour se lancer une fois par an, vous pouvez tout à fait le lancer plusieurs fois en changeant son heure/jour/mois d'exécution dans le réglage des lissages.

## J'ai eu une panne de mon Jeedom pendant 2h ce jour, puis-je rattrapper les lissages Heure non faits ?
Il n'est pas possible de rattraper ce qui n'a pas été fait. C'est pourquoi il est très important de configurer des lissages en cascade en ajoutant par exemple un lissage par jour en plus du lissage par heure et éventuellement un lissage par semaine même si c'est avec les mêmes paramètres (mode, arrondi, intervalle) afin d'être sûr que l'information soit au moins traitée une fois.

## Quelle est la configuration idéale de Jeedom pour utiliser ce plugin ?
Le mieux est de garder l'archivage à 02h00 avec un délai avant archivage de 2h. D'avoir la sauvegarde qui se lance à 05h25. À partir de là réglez les lissages pour que le jour se lance à 01h00 (avant l'archivage donc), la semaine à 03h00, le mois à 04h00 (avant la sauvegarde).  Pour l'année cela n'a pas trop d'importance, ça peut être 05h00 ou n'importe quelle autre heure disponible.  
Eviter les configurations bizarres comme par exemple un délai d'archivage de 24h...

## Quel est l'intérêt d'utiliser HistoLisse ?
Avec plus de 600 heures de développement il faut espérer qu'il y ait un véritable intérêt !  
La problématique, comme expliqué plus haut, est que Jeedom ne propose que : de ne faire aucun lissage / ou bien de ne conserver qu'un seul enregistrement par heure. Dans le premier cas on a très vite une base de données énorme et dans le 2nd cas on peut manquer d'informations utiles à long terme.

Pour beaucoup de commandes 1 enregistrement par heure est suffisant. Mais ça peut être frustrant quand il s'agit de commande qu'on veut comparer d'un mois ou année sur l'autre. Essentiellement toutes les commandes de consommation de courant ou de production solaire dont on veut garder un historique précis mais léger, toutes les 15 Min ou 30 Min pendant plusieurs mois. HistoLisse permet cela.  
A l'inverse il permet aussi de ne garder qu'un enregistrement toutes les 6 heures, par exemple, sur du long terme soit 4 par jour au lieu de 24.

L'autre intérêt est pour les commandes du genre Zigbee (sur courant) ou Téléinfo qui ont tendance à envoyer des données toutes les 2 à 8 secondes ce qui surcharge très vite les tables et ralentit d'autant l'affichage des graphiques.  
En traitant chaque heure ces commandes avec un intervalle à la minute on réduit énormément la taille de la table history et donc le temps de chargement des graphiques.

Parfois sur ces commandes on a besoin de cette haute fréquence d'enregistrements pour faire des calculs en direct comme par exemple du délestage ou des choses comme ça. HistoLisse permet cela aussi via l'âge des données : On peut ne pas traiter la dernière minute ou les 10 dernières minutes etc d'une commande et donc à la fois réduire le nombre d'enregistrements de la journée tout en gardant une information précise en live.

## Où sont stockées les données ?
→ Dans le dossier `data` du plugin, via des fichiers json qu'il est fortement conseillé de ne pas modifier !

[🔙 Retour au sommaire](index.md)
