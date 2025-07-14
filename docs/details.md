[🔙 Retour au sommaire](index.md)

## activer
![Config](img/conf.png)

Une fois installé, activer le plugin via la page **configuration**.  
N'hésitez pas à consulter la [FAQ](faq.md) pour des explications plus techniques.

## accueil
![Accueil](img/acc.png)
Il y a 4 pages principales, les pages Diagnostic et Réglage des commandes étant celles que vous fréquenterez le plus.  
Il y a aussi la possibilité d'ouvrir un nouveau sujet sur la section du forum Jeedom dédiée à ce plugin.

## diagnostic
![dg-resumé](img/dg-res.png)
Jetez un oeil sur l'onglet résumé de la page **Diagnostic** pour vérifier votre configuration et les commandes gourmandes.  
Vous retrouvez ici l'état des tables et leur évolution dans le temps. Ainsi qu'un résumé Top15 des commandes occupants le plus de place dans les tables history et historyArch.

Quoi que vous fassiez, il y aura toujours des commandes avec un "volume élevé", c'est normal puisque ce volume est calculé par rapport à la moyenne.

Comme partout dans le plugin les tables sont triables en cliquant sur les en-têtes de colonnes.
  
🧠 Lorsqu'une mise en cache existe comme ici et sur d'autres pages, il y a la possibilité, via le bouton rafraîchir, de renouveler ce cache.

## lissages
![crons](img/crons.png)
Régler les horaires de traitement via Réglage des Lissages.  
À chaque à chaque sauvegarde les dates de prochaine exécution sont mises à jour afin de contrôler que cela correspond à ce que vous voulez. Prenez en compte votre configuration Jeedom pour éviter les conflits de traitement et les lenteurs.

## gestion
![gestion](img/gestion.png)
Commencez par ajouter des commandes, via la **Gestion**, qui seront désormais gérées par le plugin (sauf la purge qui reste gérée par Jeedom)

Le lien direct vers l'équipement ouvre dans une nouvelle fenêtre l'équipement contenant cette commande.  
Le lien vers la fenêtre de configuration Jeedom ne permet que de modifier la purge et la répétition des valeurs. Si vous voulez modifier le reste, qui n'est ici que pour info, il faut ouvrir la page de l'équipement.

## réglages
![réglage](img/regl.png)
Ensuite ouvrez le **Réglage des commandes** pour configurer chaque commande.

### type
![cmdr](img/cmdr.png)
Les commandes de type numérique ont plus de possibilités.
Retrouver pour chaque commande ses statistiques dans les 2 tables, les infos de base comme le délai avant la purge. La dernière valeur et sa date, en voir plus avec les 20 dernières valeurs dans chacune des tables. Un menu de navigation entre les commandes.

Ici, vous réglez les lissages pour cette commande :
- Son mode, c'est à dire la façon dont vont être agrégées les données (moyenne, maximum, minimum, etc). 
- Le nombre de décimales pour l'arrondi. 
- L'intervalle, c'est à dire le délai entre 2 points enregistrés. Retenez bien qu'un interalle d'1 minute donne 1440 enregistrement sur une seule journée. Le Plugin ne créera pas de données ! Donc si vous avez mis un intervalle par heure à 5 Min vous ne pouvez pas mettre un intervalle d'1 minute pour semaine, il sera forcément au moins égal à 5 Min ou plus.
- Pour le lissage par heure, vous pouvez décider de ne pas lisser les dernières données qui ont moins de 1 minute, 10 minutes, 1 h etc, en réglant l'âge des données.
- Pour les lissages, semaine, mois et année, il y a en plus le jour de fin à régler. Sachant que le jour de début de la plage sera automatiquement assigné en fonction du lissage. Les dates correspondantes sont indiquées en dessous à chaque fois que vous modifiez le jour de fin.

Si des lissages ne sont pas proposés, c'est en raison du délai de purge (par exemple, si purge=7 jours, vous ne verrez pas le lissage semaine).  
En fonction des stats et infos, des conseils sont donnés par Hector.

⚠️ *Vous aurez toujours des commandes en rouge ou en orange parce qu'il y a forcément des commandes avec plus de données que la moyenne. Cela reste une indication*.

![cmdb](img/cmdb.png)
Les commandes de type binaire sont limitées sur les modes.  
Valeur la plus proche signifie au plus proche de l'intervalle : s'il est de 5 min on gardera les points à h00 h05 h10... c'est la donnée historisée le plus proche de cette minute qui sera donc conservée.

### détails
![dg-détails](img/dg-det.png)
Via **Diagnostic** vous avez accès aux détails de toutes les commandes historisées.  
Vous retrouverez la taille totale de tous les enregistrements et le détail par table, également les infos Jeedom et vous pouvez trier via les entêtes et filtrer.

### backups
![dg-backup](img/dg-back.png)
Si vous avez fait une erreur vous pouvez voir et restaurer un ancien réglage Via Diagnostic onglet Backups.  
Les backups sont gérés de façon incrémentielle mais il y aura toujours les 2 plus récents.

### historique
![dg-lissage](img/dg-liss.png)
Consultez Via Diagnostic le résultat des lissages (autre que par heure). L'historique permet de garder une trace des grands lissages ainsi que le cumul pour chaque commande traitée par mois pour les 3 derniers mois.

[🔙 Retour au sommaire](index.md)