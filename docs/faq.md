[🔙 Retour au sommaire](index.md)

# ❓ FAQ

### Comment fonctionne l'historique de Jeedom ?
→ Vos appareils renvoie des états et des informations que vous pouvez historiser = garder une trace dans la base de données.
Par exemple : une prise connectée indique le voltage actuel "220,15V" toutes les 20 secondes. 
- Ce chiffre est stocké dans la table `history` de la base de données qui contient toutes les données reçues pour la journée en cours. Avec un retour toutes les 20 secondes, il y aura en fin de journée, après 24h, **4320** enregistrements pour le voltage de cette prise (sauf si vous avez configuré "Limiter à une valeur toute les" dans la commande).
- Chaque jour (par défaut à 2h00 : cron archive dans le moteur de tâches) Jeedom fait un `archivage` (ou lissage de la nuit) de cette table, c'est à dire qu'il transfère l'ensemble des données vers une seconde table `historyArch` en appliquant un lissage, si vous l'avez configuré (moyenne, minimum etc), qui ne garde alors que 1 seul enregistrement par heure, ce qui réduit à 24 au lieu de 4320 les enregistrements pour la commande voltage de la prise. Si vous n'avez rien configuré les 4320 sont transférés tel quels.
- NB : L'archivage transfère toutes les données de la table history (les dernières 24h) comme il a lieu à 2h du matin ça inclue aussi les dernières données entre minuit et 2h00 sauf si vous avez réglé le délai d'archivage sur 2h (fortement conseillé dans réglages/système/configuration/équipements : Délai avant archivage) auquel cas il prendra bien tout de 0h00 à minuit.
- Ensuite, en général à 05h25, Jeedom réalise une `sauvegarde` (cron backup dans le moteur de tâches) de tout votre Jeedom y compris la base de données.

### Où sont stockées les données ?
→ Dans le dossier `data` du plugin, via des fichiers json qu'il est fortement conseillé de ne pas modifier !

[🔙 Retour au sommaire](index.md)
