# CHANGELOG

## [1.3] - 2025-08-12

### Ajouts
- dans **Réglage des lissages** ajout d'un bouton de dérogation "Lancer immédiatement" pour chaque période qui permet de lancer un lissage général en dehors des horaires prévus et pour toutes les commandes qui ont un réglage activé pour cette période.
- dans **Réglage des commandes** ajout d'un bouton de dérogation "Lancer immédiatement tous ces lissages" qui permet de lancer tous les lissages activés et pour cette commande uniquement, en dehors des horaires prévus.
- correction d'un bug d'url trop longue qui empêchait l'accès aux réglages en cas de nombreuses commandes gérées (+ de 100).
- diverses corrections et optimisations mineures.

### Ajouts précédents
- page principale : afficher l'état des tables en temps réel, le dernier lissage et les Tops commandes (cache)
- 2025-07-15 1ère Version Stable
- lissage par année
- compatibilité php7.3+
- Hector et ses synonymes
- masquage des lissages suivant le délai de purge
- log/backup des commandes dans diagnostic
- log des lissages dans diagnostic