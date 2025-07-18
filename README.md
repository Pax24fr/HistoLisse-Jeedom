# Plugin Histolisse pour Jeedom

## DESCRIPTION
Histolisse est un plugin Jeedom permettant de lisser les données historiques des commandes pour optimiser la taille des tables `history` et `historyArch`. Il propose des lissages précis par heure, jour, semaine, mois et année.

## INSTALLATION
1. Installez le plugin via le Market Jeedom.
2. Activez le plugin dans la configuration.

## PRISE EN MAIN
1. Vérifiez avec **Diagnostic** que votre configuration est correcte et surveillez l'évolution des tables, les lissages effectuées, gérez les données orphelines.
2. Réglez vos heures de lissage via **Réglage des Lissages**.
3. Utilisez **Gestion des commandes** pour ajouter/retirer des commandes à lisser parmi celles déjà historisées dans Jeedom.
4. Activez les options de lissage dans **Réglages des commandes** (heure, jour, semaine, mois, année) et configurez précisément le lissage (mode, arrondi, intervalle, âge des données, période...)

## LES LISSAGES
- par Heure : pour réduire la taille de la table history en journée tout en gardant des données utiles, exemple pour un voltage : 1 enregistrement par minute pour la journée en cours. 
- par Jour : pour réduire la taille de la table historyArch avant l'archivage Jeedom et la sauvegarde quotidienne en gardant moins d'informations, pour ce voltage : 1 enr./15mn pour les jours passés.
- par Semaine : pour supprimer des informations inutiles à moyen terme dans la table historyArch, pour ce voltage : 1 enr./heure pour les dates au delà de 7 jours.
- par Mois : pour supprimer des informations inutiles à long terme dans la table historyArch, pour ce voltage : 1 enr./6h pour les dates au delà de 6 mois.
- par Année : pour supprimer des informations inutiles à très long terme dans la table historyArch, pour ce voltage : 1 enr./jour pour les dates au delà de 1 an.

### PRÉ-REQUIS
- Jeedom >= 4.0, php 7.3+
- Seules les commandes déjà indiquées comme historisées dans Jeedom seront gérées par ce plugin.
- La purge (effacement des données après x temps) reste gérée par Jeedom.

### SUPPORT
[Documentation](https://pax24fr.github.io/HistoLisse-Jeedom/)  

Créez un post sur le [Community Jeedom](https://community.jeedom.com/) avec le tag `plugin-histolisse` pour toute question.
Si vous constatez des incohérences dans les calculs ou affichages, désactivez ce plugin pour confirmer qu’il n’en est pas la cause.