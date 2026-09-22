# Intent : Simulateur Mars Rover

Auteur : Julie Roget (développeuse).

## Problème
L'équipe Mars Rover a besoin de valider la logique de déplacement du rover avant de la confronter au matériel réel.

## Résultat proposé
Un simulateur qui reçoit un point de départ, une carte et une liste de commandes, interprète ces commandes, puis affiche la position et la direction finales du rover.

## Utilisateurs et systèmes concernés
Équipe de développement Mars Rover.

## Contraintes
- Le simulateur reçoit un point (x, y), une orientation N, S, E ou W, une carte qui place les obstacles et une liste de commandes.
- Le rover peut avancer ou tourner de 90 degrés à droite ou à gauche.
- Il reste immobile lorsqu'un obstacle bloque son avancée.
- La carte peut employer les symboles 🟩 et 🌳 ou les symboles 🟫 et 🪨.

## Questions ouvertes
- Quelles sont les dimensions/limites de la carte, et que se passe-t-il en bord de carte (mur, erreur, autre) ?
- Quel est le format exact d'entrée (fichier, ligne de commande, API...) et de sortie affichée ?
- Une commande invalide ou une combinaison de symboles de carte inattendue doit-elle être gérée d'une façon particulière ?
- Y a-t-il un langage ou une technologie imposés pour l'implémentation ?
