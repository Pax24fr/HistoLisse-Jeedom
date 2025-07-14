[🔙 Retour au sommaire](index.md)
# ⚙️ FONCTIONNALITÉS

- Lissage des données par : heure / jour / semaine / mois / année
- Diagnostics des données en base
- Sauvegardes automatiques des confiurations
- Interface Jeedom moderne

## PRISE EN MAIN
1. Vérifiez avec **Diagnostic** que votre configuration est correcte et surveillez l'évolution des tables, les lissages effectuées, gérez les données orphelines.
2. Réglez vos heures de lissage via **Réglage des Lissages**.
3. Utilisez **Gestion des commandes** pour ajouter/retirer des commandes à lisser parmi celles déjà historisées dans Jeedom.
4. Activez les options de lissage dans **Réglages des commandes** (heure, jour, semaine, mois, année) et configurez précisément le lissage (mode, arrondi, intervalle, fréquence non lissée, période...)

## REGLAGE DES COMMANDES
- Mode de lissage suivant le type de la commande (numeric, binary, autre).
- Moyenne (des valeurs)
- Minimum (garder la valeur la plus basse)
- Maximum (garder la valeur la plus haute)
- Valeur la plus proche (de la minute de l'intervalle)
- Arrondi : Nombre de décimales, doit être inférieur ou égal à l'arrondi précédent
- Intervalle en minutes entre les points lissés. Ex. : 5 pour un point toutes les 5 minutes. Doit être égal ou supérieur à l'intervalle précédent
- Âge des données (Bloc non lissé) : Les données traitées seront plus âgées que cette durée. Ex1 : + de 4h => à 9h on lissera les données de 4h00 à 4h59, les dernières données entre 5h00 et 9h00 (bloc de 4h) ne seront pas encore lissées. Ex2 : + de 1 minute => à 9h on lissera les données de 7h59 à 8h58 inclus.
- Jour (Début et) Fin de la plage de données à lisser à la date d'exécution programmée.
    Début : calculé automatiquement suvant le jour de Fin (-6 pour semaine, -30 pour mois et -366 pour année).
    ex : -8 pour 1 semaine avant, -31 pour 1 mois avant. Doit être inférieur ou égal à -1 (conseillé -7 et -31)

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

[🔙 Retour au sommaire](index.md)
