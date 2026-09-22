# Spec : Simulateur Mars Rover

Intention de référence : intent/mars-rover-simulator/intent.md

## Périmètre

Besoin couvert : simuler le déplacement d'un rover sur une carte, à partir d'un point de départ, d'une orientation initiale et d'une liste de commandes (avancer, tourner à droite ou à gauche de 90°), en tenant compte des obstacles présents sur la carte, puis afficher la position et l'orientation finales du rover.

Exclusions : l'intention n'indique aucune exclusion explicite.

## Exigences

### EX-01 — Initialisation du rover

Origine dans l'intention : « Le simulateur reçoit un point (x, y), une orientation N, S, E ou W, une carte qui place les obstacles et une liste de commandes. » (Contraintes)
Comportement attendu : avant d'interpréter la première commande, le simulateur positionne le rover au point (x, y) reçu, avec l'orientation reçue (N, S, E ou W).

Scénario
- Situation de départ : le point de départ (x, y), l'orientation N/S/E/W, la carte et la liste de commandes sont fournis via l'interface web de l'artefact (formulaire/zone de texte).
- Action : le simulateur démarre la simulation.
- Résultat attendu : le rover est positionné en (x, y) avec l'orientation donnée avant toute commande.

### EX-02 — Avancer en ligne droite

Origine dans l'intention : « Le rover peut avancer ou tourner de 90 degrés à droite ou à gauche. » (Contraintes)
Comportement attendu : une commande d'avance déplace le rover d'une case dans la direction de son orientation courante, si cette case n'est pas un obstacle.

Scénario
- Situation de départ : le rover est en (x, y), orienté N ; la case suivante dans la direction N n'est pas un obstacle.
- Action : le simulateur interprète une commande d'avance.
- Résultat attendu : le rover se trouve sur la case suivante dans la direction N ; son orientation reste N.

Une commande d'avance non reconnue par le simulateur est ignorée : la simulation se poursuit avec la commande suivante sans modifier l'état du rover.

### EX-03 — Tourner de 90° à droite ou à gauche

Origine dans l'intention : « Le rover peut avancer ou tourner de 90 degrés à droite ou à gauche. » (Contraintes)
Comportement attendu : une commande de rotation change l'orientation du rover de 90° vers la droite ou vers la gauche, sans modifier sa position.

Scénario
- Situation de départ : le rover est en (x, y), orienté N.
- Action : le simulateur interprète une commande de rotation à droite.
- Résultat attendu : le rover reste en (x, y), désormais orienté E. (Le même principe s'applique symétriquement à une rotation à gauche.)

Une commande de rotation non reconnue par le simulateur est ignorée : la simulation se poursuit avec la commande suivante sans modifier l'état du rover.

### EX-04 — Immobilisation face à un obstacle

Origine dans l'intention : « Il reste immobile lorsqu'un obstacle bloque son avancée. » (Contraintes)
Comportement attendu : si la case suivante dans la direction courante est un obstacle, une commande d'avance ne déplace pas le rover.

Scénario
- Situation de départ : le rover est en (x, y), orienté N ; la case suivante dans la direction N est un obstacle.
- Action : le simulateur interprète une commande d'avance.
- Résultat attendu : le rover reste en (x, y) ; son orientation est inchangée.

Ce même comportement s'applique lorsque la case suivante se situerait hors des limites de la carte : l'avancée est refusée comme devant un obstacle, et le rover reste immobile (voir Réserve R-01, tranchée).

### EX-05 — Carte à deux jeux de symboles équivalents

Origine dans l'intention : « La carte peut employer les symboles 🟩 et 🌳 ou les symboles 🟫 et 🪨. » (Contraintes)
Comportement attendu : le simulateur interprète une carte utilisant soit le jeu 🟩 (libre) / 🌳 (obstacle), soit le jeu 🟫 (libre) / 🪨 (obstacle), avec la même sémantique dans les deux cas.

Scénario
- Situation de départ : une carte est fournie avec le jeu de symboles 🟫 (libre) / 🪨 (obstacle).
- Action : le simulateur charge la carte.
- Résultat attendu : chaque case 🪨 est traitée comme un obstacle et chaque case 🟫 comme un terrain libre, exactement comme le seraient 🌳 et 🟩 sur une carte utilisant l'autre jeu de symboles.

Un symbole de carte inattendu (ne faisant partie d'aucun des deux jeux) est ignoré : son chargement ne provoque pas d'arrêt de la simulation.

### EX-06 — Affichage du résultat final

Origine dans l'intention : « […] un simulateur qui […] affiche la position et la direction finales du rover. » (Résultat proposé)
Comportement attendu : une fois la liste de commandes entièrement interprétée, le simulateur affiche la position (x, y) et l'orientation finales du rover.

Scénario
- Situation de départ : la liste de commandes a été entièrement interprétée par le simulateur.
- Action : le simulateur termine la simulation.
- Résultat attendu : le simulateur affiche la position et l'orientation finales du rover à l'écran, dans l'interface web de l'artefact.

## Conception proposée

- Modèle de domaine (accepté, découle directement des contraintes) : un état Rover (position (x, y) + orientation N/S/E/W) et une Carte représentée comme une grille de cases, chacune libre ou occupée par un obstacle.
- Moteur d'interprétation (accepté, découle des contraintes sur l'avancée et la rotation) : les commandes sont interprétées une à une dans l'ordre de la liste ; une commande d'avance ne déplace le rover que si la case suivante dans la direction courante n'est pas un obstacle ; une commande de rotation ne modifie que l'orientation, jamais la position.
- Équivalence des jeux de symboles (accepté, contrainte explicite) : la carte accepte indifféremment le jeu 🟩/🌳 ou le jeu 🟫/🪨 pour représenter respectivement une case libre et un obstacle, sans différence de comportement entre les deux jeux.
- Séparation de la logique de simulation et des entrées/sorties (proposition à valider) : isoler l'interprétation des commandes de l'interface web de l'artefact (formulaire de saisie et affichage du résultat), afin que la logique de simulation reste testable indépendamment de cette interface.
- Langage et technologie d'implémentation (accepté, R-04 tranchée) : le simulateur est implémenté en HTML/JavaScript/CSS, sous une forme compatible avec les artefacts Claude Code (web) — un fichier autonome, exécutable dans le navigateur, sans étape de compilation ni dépendance serveur.
- Format d'entrée et de sortie (accepté, R-02 tranchée) : le point de départ, la carte et la liste de commandes sont saisis via une interface web (formulaire/zone de texte) intégrée à l'artefact ; le résultat (position et orientation finales) est affiché à l'écran dans cette même interface.

## Réserves

### R-01 — Limites de la carte et comportement en bord de carte

Origine : question ouverte de l'intention (« Quelles sont les dimensions/limites de la carte, et que se passe-t-il en bord de carte (mur, erreur, autre) ? »).
Exigences concernées : EX-02, EX-04.
Conséquences : sans réponse, il n'est pas possible de préciser si une avancée qui sortirait de la carte est bloquée comme devant un obstacle, provoque une erreur, fait apparaître le rover de l'autre côté de la carte, ou correspond à un autre comportement.
Décision attendue : le Product Owner doit préciser comment la carte définit ses limites et le comportement attendu du rover lorsqu'une avancée le mènerait hors de ces limites.
Décision : une avancée qui mènerait le rover hors des limites de la carte est bloquée, exactement comme devant un obstacle ; le rover reste immobile.
Auteur : Julie Roget (Product Owner). Date : 2026-09-22.
Statut : tranchée.

### R-02 — Format d'entrée et de sortie du simulateur

Origine : question ouverte de l'intention (« Quel est le format exact d'entrée (fichier, ligne de commande, API...) et de sortie affichée ? »).
Exigences concernées : EX-01, EX-06.
Conséquences : sans réponse, l'interface du simulateur (comment il reçoit le point de départ, la carte et les commandes ; comment il affiche le résultat) ne peut pas être précisée.
Décision attendue : le Product Owner doit préciser le format d'entrée et de sortie attendu.
Décision : le point de départ, la carte et la liste de commandes sont saisis via une interface web (formulaire/zone de texte) intégrée à l'artefact ; le résultat est affiché à l'écran dans cette même interface. Décision prise avec la Réserve R-04, la technologie retenue (artefact Claude Code) rendant caduque une saisie en ligne de commande.
Auteur : Julie Roget (Product Owner). Date : 2026-09-22.
Statut : tranchée.

### R-03 — Gestion des commandes invalides et des symboles de carte inattendus

Origine : question ouverte de l'intention (« Une commande invalide ou une combinaison de symboles de carte inattendue doit-elle être gérée d'une façon particulière ? »).
Exigences concernées : EX-02, EX-03, EX-05.
Conséquences : sans réponse, le comportement du simulateur face à une commande non reconnue ou un symbole de carte inattendu n'est pas défini (ignorer, arrêter la simulation, signaler une erreur...).
Décision attendue : le Product Owner doit préciser le comportement attendu dans ces cas.
Décision : une commande non reconnue ou un symbole de carte inattendu est ignoré ; la simulation se poursuit sans s'arrêter et sans modifier l'état du rover pour cet élément.
Auteur : Julie Roget (Product Owner). Date : 2026-09-22.
Statut : tranchée.

### R-04 — Langage ou technologie imposés

Origine : question ouverte de l'intention (« Y a-t-il un langage ou une technologie imposés pour l'implémentation ? »).
Exigences concernées : aucune exigence fonctionnelle directement ; conditionne le choix technique dans la Conception proposée.
Conséquences : sans réponse, la phase Build ne peut pas choisir un langage ou un environnement d'implémentation en connaissance de cause.
Décision attendue : le Product Owner doit préciser si une contrainte de langage ou de technologie s'impose.
Décision : le simulateur doit être implémenté avec une stack compatible avec les artefacts Claude Code (web), c'est-à-dire en HTML/JavaScript/CSS, exécutable dans le navigateur sans dépendance serveur.
Auteur : Julie Roget (Product Owner). Date : 2026-09-22.
Statut : tranchée.

## Questions ouvertes

- Quelles sont les dimensions/limites de la carte, et que se passe-t-il en bord de carte (mur, erreur, autre) ? — Répondu par le Product Owner (voir Réserve R-01) : une avancée hors des limites de la carte est bloquée comme devant un obstacle. Le passage en Build n'est plus bloqué sur ce point.
- Quel est le format exact d'entrée (fichier, ligne de commande, API...) et de sortie affichée ? — Répondu par le Product Owner (voir Réserve R-02) : saisie et affichage via l'interface web de l'artefact. Le passage en Build n'est plus bloqué sur ce point.
- Une commande invalide ou une combinaison de symboles de carte inattendue doit-elle être gérée d'une façon particulière ? — Répondu par le Product Owner (voir Réserve R-03) : commande ou symbole inattendu ignoré, simulation non interrompue. Le passage en Build n'est plus bloqué sur ce point.
- Y a-t-il un langage ou une technologie imposés pour l'implémentation ? — Répondu par le Product Owner (voir Réserve R-04) : stack compatible avec les artefacts Claude Code (HTML/JavaScript/CSS). Le passage en Build n'est plus bloqué sur ce point.

Toutes les questions ouvertes de l'intention ont reçu une réponse du Product Owner ; aucune ne bloque plus le passage en phase Build.

## Contexte de génération

### Demande initiale

Commande `/spec intent/mars-rover/intent.md`. Le chemin fourni ne correspond à aucun fichier du dépôt ; l'unique intention présente est `intent/mars-rover-simulator/intent.md`, dont la version acceptée (fusionnée via la pull request #1, mergée par julieroget) a été utilisée pour cette spécification.

### Skills utilisées

| Chemin | Commit Git de la version utilisée |
| --- | --- |
| .claude/skills/spec/SKILL.md | 64e3fde97d7af5ce8ae6a307b9a680acd8a23967 |

### Révisions

Aucune révision à ce jour.
