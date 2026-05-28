# Changelog EventTranslator

## v1.0.7 (2026-05-28)

### Corrections

- Champ **Option** des commandes de type *slider* passé en texte libre : accepte désormais aussi bien des valeurs numériques (ex: `200`) que des valeurs hex (ex: `#d4cfbc` pour color_temp)
- Suppression de l'auto-préfixe `#` : la valeur est transmise telle quelle à la commande cible — l'utilisateur saisit exactement ce qu'attend le plugin

## v1.0.6 (2026-05-28)

### Améliorations

- Champ Option dynamique selon le type de commande : label et placeholder adaptés, input numérique pour slider, color picker pour color, champ masqué pour binary
- Bouton Tester : format JSON correct pour tous les subtypes (slider, color, select…)

## v1.0.5 (2026-05-28)

### Corrections

- Bouton **Tester** : la valeur Option (ex: slider) est correctement transmise à la commande cible

## v1.0.4 (2026-05-28)

### Améliorations

- Label **Option** affiché devant le champ de valeur des commandes non-message (slider, toggle…)

## v1.0.3 (2026-05-28)

### Améliorations

- Labels **Sujet** et **Corps** affichés devant les champs correspondants pour les commandes de type message

## v1.0.2 (2026-05-28)

### Améliorations

- Commandes de type **message** : affichage de deux champs distincts **Sujet** et **Corps** dans la table de correspondance
- Bouton **Tester** : envoie correctement sujet et corps pour les commandes de type message
- Exécution réelle via le listener : sujet et corps transmis correctement au plugin cible

## v1.0.1 (2026-05-28)

### Améliorations

- Bouton **Tester** ajouté sur chaque ligne de type *Commande* : exécute la commande directement depuis l'interface de configuration
- Titres (tooltips) ajoutés sur les boutons de sélection de commande et de scénario

## v1.0.0 (2026-05-14)

Première version publiée sur le market Jeedom.

### Fonctionnalités

- Ajout d'un équipement source via sélecteur Jeedom standard
- Surveillance de commandes info source (tout plugin compatible)
- Table de correspondance valeur source → action cible :
  - **Valeur** : mise à jour d'une commande info native de l'équipement `_et`
  - **Commande** : exécution directe d'une commande action
  - **Scénario** : lancement immédiat d'un scénario
- **Mode apprentissage** : détection automatique des valeurs en temps réel (countdown 30 s, déduplication)
- Ajout manuel de règles toujours disponible
- Répétition d'événements garantie même si la valeur ne change pas entre deux appuis
- Interface tuiles Jeedom standard : bouton Ajouter (vert) et Configuration (clé)
- Icônes des équipements sources affichées sur les tuiles
- Aucune dépendance de plugin (pas de plugin Virtuel requis)
- Requiert Jeedom 4.2 minimum
