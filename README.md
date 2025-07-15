# Plugin Histolisse pour Jeedom

## DESCRIPTION
Histolisse est un plugin Jeedom permettant de lisser les données historiques des commandes pour optimiser la taille des tables `history` et `historyArch`. Il propose des lissages par heure, jour, semaine, mois et année.

## INSTALLATION
1. Installez le plugin via le Market Jeedom.
2. Activez le plugin dans la configuration.

## PRISE EN MAIN
1. Vérifiez avec **Diagnostic** que votre configuration est correcte et surveillez l'évolution des tables, les lissages effectuées, gérez les données orphelines.
2. Réglez vos heures de lissage via **Réglage des Lissages**.
3. Utilisez **Gestion des commandes** pour ajouter/retirer des commandes à lisser parmi celles déjà historisées dans Jeedom.
4. Activez les options de lissage dans **Réglages des commandes** (heure, jour, semaine, mois, année) et configurez précisément le lissage (mode, arrondi, intervalle, fréquence non lissée, période...)

## EXECUTIONS
- Heure d'exécution du lissage jour (conseillé : 01h pour 01h01)
- Heure d'exécution du lissage semaine (conseillé : 03h pour 03h01)
- Heure d'exécution du lissage mois (conseillé : 04h pour 04h01) + Jour du mois d'exécution (conseillé 1). Si 29, 30, 31 → executé le dernier jour du mois si moins de jours.
- Heure d'exécution du lissage année (conseillé : 05h pour 05h01) + Jour du mois et mois d'exécution (conseillé 1/1).

## LISSAGES
- Le lissage par heure est idéal pour réduire la taille de la table history en journée tout en gardant des données utiles, exemple pour un voltage : 1 enregistrement par minute pour la journée en cours. 
- Le lissage par jour convient pour réduire la taille de la table historyArch avant l'archivage Jeedom et le backup quotidien en gardant moins d'informations, exemple pour un voltage : 1 enregistrement par 15mn pour les jours passés.
- Le lissage par semaine permet de supprimer des informations inutiles à moyen terme dans la table historyArch, exemple pour un voltage : 1 enregistrement par heure pour les dates au delà de 7 jours.
- Le lissage par mois permet de supprimer des informations inutiles à long terme dans la table historyArch, exemple pour un voltage : 1 enregistrement par 6h pour les dates au delà de 6 mois.
- Le lissage par année permet de supprimer des informations inutiles à long terme dans la table historyArch, exemple pour un voltage : 1 enregistrement par jour pour les dates au delà de 1 an.

### PRÉ-REQUIS
- Jeedom >= 4.0
- Vous devez avoir réglé la partie "Historique" de la configuration de la commande dans Jeedom pour qu'elle apparaisse dans "Gestion des commandes" :
    - Historiser doit être coché.
    - Mode de lissage Si défini (ex "Moyenne"), il sera remplacé par "Aucun" tant que la commande sera gérée par HistoLisse et reviendra à votre définition initiale si vous retirez la commande de cette gestion.
    - Limiter à une valeur toute les n'est plus pris en compte pour les commandes gérées par Histolisse.
    - Purger historique reste géré par Jeedom suivant votre réglage (7 jours, 3 mois etc.), est indiqué ici pour information.

### SUPPORT
Créez un post sur le [Community Jeedom](https://community.jeedom.com/) pour toute question.
Si vous constatez des incohérences dans les calculs ou affichages, désactivez ce plugin pour confirmer qu’il n’en est pas la cause.