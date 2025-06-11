# Plugin Histolisse pour Jeedom

## Description
Histolisse est un plugin Jeedom permettant de lisser les données historiques des commandes pour optimiser la taille des tables `history` et `historyArch`. Il propose des lissages horaire, journalier, hebdomadaire et mensuel.

## Installation
1. Installez le plugin via le Market Jeedom.
2. Activez le plugin dans la configuration.
3. Configurez les commandes à lisser dans la section "Gestion des commandes".

## Prise en main
1. Vérifiez avec **Diagnostic** que votre configuration est correcte et surveillez l'évolution.
2. Utilisez **Gestion des commandes** pour ajouter/retirer des commandes à lisser parmi celles déjà historisées dans Jeedom.
3. Réglez vos heures de lissage via **Réglages des crons**.
4. Activez les options de lissage dans **Réglages des commandes** (horaire, journalier, hebdomadaire, mensuel)

## REGLAGES CMD
- Mode de lissage suivant le type de la commande (numeric, binary, autre).
- Moyenne (des valeurs)
- Minimum (garder la valeur la plus basse)
- Maximum (garder la valeur la plus haute)
- Valeur la plus proche (de la minute de l'intervalle)
- Arrondi : Nombre de décimales, doit être inférieur ou égal à l'arrondi précédent
- Intervalle en minutes entre les points lissés. ex. : 5 pour un point toutes les 5 minutes. Doit être supérieur ou égal à l'intervalle précédent
- Âge des données Bloc non lissé : Les données traitées seront plus âgées que cette durée. ex : + de 4h => à 9h01 on lissera les données de 4h00 à 4h59, les dernières données entre 5h00 et 9h01 (bloc de 4h) ne seront pas encore lissées.
- Jour (Début et) Fin de la plage de données à lisser à la date d'exécution programmée.
- Début : calculé automatiquement (Fin -6 et -30).
ex : -7 pour 1 semaine avant, -31 pour 1 mois avant. Doit être inférieur ou égal à -1 (conseillé -7 et -31)

## CRON
- Heure d'exécution du lissage journalier (conseillé : 04h pour 04h01)
- Heure d'exécution du lissage hebdomadaire (conseillé : 02h pour 02h01)
- Heure d'exécution du lissage mensuel (conseillé : 01h pour 01h01)
- Jour du mois d'exécution du lissage mensuel. 29,30, 31 executés le dernier jour du mois si moins de jours. (conseillé 1)

## Lissages
- Le lissage horaire est idéal pour réduire la taille de la table history en journée tout en gardant des données utiles, exemple pour un voltage : 1 enregistrement par minute pour la journée en cours. 
- Le lissage journalier convient pour réduire la taille de la table historyArch avant le lissage Jeedom et le backup quotidien en gardant moins d'informations, exemple pour un voltage : 1 enregistrement par 15mn pour les jours précédents.
- Le lissage hebdomadaire permet de supprimer des informations inutiles à moyen terme dans la table historyArch, exemple pour un voltage : 1 enregistrement par heure pour les dates au delà de 7 jours.
- Le lissage mensuel permet de supprimer des informations inutiles à long terme dans la table historyArch, exemple pour un voltage : 1 enregistrement par 6h pour les dates au delà de 6 mois.

# Configuration

## Pré-requis
- Jeedom >= 4.0
- Vous devez avoir réglé la partie "Historique" de la configuration de la commande dans Jeedom pour qu'elle apparaisse dans "Gestion des commandes" :
    - Historiser doit être coché.
    - Mode de lissage Si défini (ex "Moyenne"), il sera remplacé par "Aucun" tant que la commande sera gérée par HistoLisse et reviendra à votre définition initiale si vous retirez la commande de cette gestion.
    - Limiter à une valeur toute les n'est plus pris en compte pour les commandes gérées par Histolisse.
    - Purger historique reste géré par Jeedom suivant votre réglage (7 jours, 3 mois etc.), est indiqué ici pour information.

## Support
Créez un post sur le [Community Jeedom](https://community.jeedom.com/) pour toute question.