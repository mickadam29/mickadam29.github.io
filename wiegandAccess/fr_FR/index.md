# Wiegand Access

Plugin Jeedom de contrôle d'accès par badge RFID et/ou clavier PIN. Compatible avec tout équipement Jeedom disposant d'une commande événement.

---

## Présentation

**Wiegand Access** permet de gérer un ou plusieurs lecteurs de badges RFID / claviers PIN depuis Jeedom.

Le plugin est **protocole-agnostique** : il fonctionne avec n'importe quel équipement Jeedom capable d'envoyer les événements attendus — Zigbee (PTVO), Z-Wave, WiFi, MQTT ou tout autre protocole. Il suffit que les commandes existent dans Jeedom.

Un **lecteur virtuel de démonstration** est créé automatiquement à l'installation, permettant de tester toutes les fonctionnalités sans aucun matériel.

### Fonctionnalités principales

- Trois modes par lecteur : Badge RFID seul, Code PIN seul, Badge + PIN
- Enregistrement de badges par apprentissage, manuellement ou par import CSV
- Codes globaux — un badge enregistré est reconnu sur tous les lecteurs
- Planning horaire par badge (7 jours, plages horaires)
- Action associée à chaque badge (commande Jeedom ou scénario)
- Usage unique : le badge est supprimé automatiquement après utilisation
- Historique complet des accès (accordé, refusé, hors-planning)
- Export CSV — sauvegarde de tous les badges vers le PC
- Protection anti-intrusion par lecteur (compteur d'erreurs, verrouillage)
- Tuile de statut dans le dashboard Jeedom pour chaque lecteur
- Lecteur virtuel intégré pour tester le plugin sans matériel
- **Configuration automatique pour les interfaces WIEGAND ZIGBEE disponibles auprès de l'auteur du plugin**

---

## Prérequis

| Élément | Requis |
|---|---|
| Jeedom | 4.4 minimum |
| Matériel | Optionnel — un lecteur virtuel de démo est fourni |
| Plugin Zigbee2MQTT (z2m) | Optionnel — uniquement pour les interfaces WIEGAND ZIGBEE PTVO |

> Si vous utilisez les interfaces WIEGAND ZIGBEE de l'auteur, le converter `ptvo_wiegand.js` est déployé automatiquement dans le dossier `external_converters` de z2m lors de l'activation du plugin. Redémarrer Zigbee2MQTT après l'installation.

---

## Installation

1. Installer le plugin depuis le market Jeedom
2. Activer le plugin
3. Un **Lecteur démo** (virtuel) est automatiquement disponible — vous pouvez commencer à tester immédiatement
4. Pour une utilisation avec du matériel physique : appairer le ou les lecteurs dans votre plugin domotique (z2m ou autre), puis configurer les commandes

---

## Trois modes d'interface

Chaque lecteur dispose d'un **mode d'interface** qui détermine comment il reçoit ses événements.

### Mode Physique (commandes Jeedom)

Le lecteur écoute des commandes existantes dans Jeedom issues d'un équipement physique (z2m, Z-Wave, MQTT, ou tout autre plugin). Les commandes `event`, `badge_number`, `pin_number`, `CODE_OK`, `CODE_KO` etc. sont sélectionnées dans le formulaire de configuration.

C'est le mode de production pour un matériel réel.

### Mode Virtuel — Démo (simulateur intégré)

Aucun équipement physique ni scénario requis. Le lecteur propose un **simulateur intégré** directement dans l'interface du plugin :

- **Simuler un badge** — présentation d'un badge (par son numéro décimal)
- **Simuler un PIN** — saisie d'un code PIN
- **Abandon** — simulation d'une saisie abandonnée
- **Verrouiller lecteur** — simule l'ouverture du boîtier (équivalent Tamper ON)
- **Déverrouiller lecteur** — simule la refermeture (équivalent Tamper OFF)

Toute la logique du plugin est active : badges autorisés/refusés, planning horaire, compteur d'erreurs, verrouillage, historique.

> **Rôle :** bac à sable instantané pour découvrir et tester le plugin. Un lecteur démo est créé automatiquement à l'installation.

### Mode Virtuel — Entrées externes (scénario, plugin Virtuel…)

Le lecteur **n'écoute aucun équipement physique**. Il expose à la place cinq **commandes action Jeedom** que n'importe quel scénario, le plugin Virtuel, ou tout autre plugin peut appeler :

| Commande | Paramètre | Description |
|---|---|---|
| **Envoyer un badge** | Numéro du badge (décimal) | Simule la présentation d'un badge RFID |
| **Envoyer un PIN** | Code PIN | Simule la saisie d'un code PIN |
| **Envoyer Abandon** | — | Simule une saisie abandonnée |
| **Envoyer Tamper ON** | — | Simule l'ouverture du boîtier |
| **Envoyer Tamper OFF** | — | Simule la refermeture du boîtier |

Toute la logique du plugin s'applique exactement comme pour un lecteur physique : planning horaire, historique, compteur d'erreurs, verrouillage, actions associées aux badges.

> **Rôle :** intégration programmatique réelle. Idéal lorsque votre équipement envoie les données dans un format non standard — un scénario fait la conversion et appelle les commandes du lecteur.

#### Exemple — Scénario d'intégration

```
Déclencheur : [Mon équipement][badge_brut] change
Si [Mon équipement][badge_brut] != ""
  Action : [Lecteur entrées ext.][Envoyer un badge]  message = #[Mon équipement][badge_brut]#
```

Un seul bloc action suffit. Le plugin gère ensuite la vérification, l'historique et les actions associées.

---

## Configuration d'un lecteur physique

### Voie rapide — Interfaces WIEGAND ZIGBEE (PTVO) disponibles auprès de l'auteur

1. Sur la page principale du plugin, cliquer **+** pour créer un équipement
2. Renseigner le **nom** et l'**objet parent**, activer et rendre visible si souhaité
3. Choisir le **mode d'interface** : Physique
4. Choisir le **type de lecteur** : Badge RFID, Code PIN, ou Badge + PIN
5. Cliquer le sélecteur de la commande **event** et choisir l'équipement z2m correspondant
6. Le plugin détecte automatiquement l'interface PTVO et **remplit toutes les commandes**
7. Les valeurs par défaut sont appliquées : seuil d'erreurs **3**, délai DELAY_LOCKOUT **15 min**, code UNLOCK_CODE **aléatoire** (si mode PIN)
8. Vérifier et ajuster si nécessaire, puis **Sauvegarder**

> Le formulaire s'affiche progressivement : les commandes n'apparaissent qu'une fois le type de lecteur et la commande `event` choisis.

### Configuration manuelle (autre matériel)

1. Créer un équipement, renseigner le nom et l'objet parent
2. Choisir le **mode d'interface** : Physique
3. Choisir le **type de lecteur**
4. Sélectionner la commande **event** depuis l'équipement correspondant
5. Sélectionner les commandes de réception (`badge_number`, `pin_number` selon le type)
6. (Optionnel) Configurer les commandes de retour : `CODE_OK`, `CODE_KO`, `DELAY_LOCKOUT`, `UNLOCK_CODE`
7. (Optionnel) Configurer les commandes de verrouillage physique : `LOCK`, `LOCK_UNTIL`, `UNLOCK`
8. (Optionnel) Configurer les actions sur événements **Tamper** et **Abandon**
9. Sauvegarder

### Commandes créées automatiquement à la sauvegarde

| Commande | Type | Description |
|---|---|---|
| `access_granted` | info/binaire | Passe à 1 lors d'un accès autorisé |
| `access_denied` | info/binaire | Passe à 1 lors d'un accès refusé |
| `last_label` | info/string | Libellé du dernier badge ou PIN présenté |
| `reader_panel` | info/string | Tuile de statut (widget dashboard) |

---

## Enregistrement d'un badge ou d'un code PIN

### Méthode recommandée — Enregistrement global (apprentissage)

1. Depuis la page principale du plugin, activer le curseur **Inscription Badge / Code**
2. Choisir la durée d'écoute (1 à 15 min, défaut 5 min) — un décompte s'affiche
3. **Première présentation** : présenter le badge (ou saisir le PIN) sur n'importe quel lecteur actif → le lecteur émet un **bip KO** (badge reçu, en attente de confirmation)
4. **Attendre le signal de confirmation** : après quelques secondes, le lecteur émet un **bip OK** et l'indicateur **↩** apparaît dans la tuile
5. **Deuxième présentation** : présenter à nouveau le même badge → bip OK, enregistrement confirmé, mode inscription désactivé automatiquement
6. La liste des badges s'ouvre, le nouveau badge est **surligné en jaune**

> La double présentation évite les inscriptions accidentelles.

### Enregistrement manuel (numéro connu)

Utile lorsque le numéro du badge est connu à l'avance (lu avec un ACR122U par exemple) et que le badge doit être opérationnel immédiatement.

1. Depuis la page principale, cliquer **Enregistrement manuel**
2. Choisir le **type** : Badge RFID ou Code PIN
3. Saisir le **code** (numéro décimal)
4. Saisir un **libellé** (optionnel)
5. Cliquer **Enregistrer** → opérationnel immédiatement sur tous les lecteurs

> Le numéro décimal lu par un ACR122U (`nfc-list`) correspond directement au code à saisir.

### Import CSV

Permet d'importer une liste de badges en une seule opération.

**Format du fichier :**
```
code,type,label
3322029316,badge,Badge Bleu
1049779041,badge,Badge Noir
971002,pin,Code PIN Bureau
```

1. Depuis la page principale, cliquer **Import CSV**
2. Sélectionner le fichier CSV
3. Vérifier la prévisualisation (colonne Statut : OK / Invalide)
4. Cliquer **Importer**

> Si un badge existe déjà (même numéro), son libellé est conservé — l'import est idempotent.

### Export CSV

1. Depuis la page principale, cliquer **Export CSV**
2. Le fichier `wiegandAccess_badges_YYYY-MM-DD.csv` est téléchargé dans le répertoire par défaut du navigateur

> Pour choisir l'emplacement à chaque export : activer **"Toujours demander où enregistrer"** dans les préférences du navigateur.

Le fichier exporté peut être réimporté tel quel — il sert de sauvegarde complète.

---

## Gestion des badges

Accéder à la liste via le bouton **Badges** sur la page principale.

| Colonne | Description |
|---|---|
| Type | Badge RFID ou PIN |
| Code | Identifiant brut |
| Libellé | Nom libre (ex. "Jean Dupont") |
| Action | Commande Jeedom ou scénario déclenché à chaque accès autorisé |
| Actif | Activer / désactiver ce badge sur tous les lecteurs |
| Usage unique | Supprimé automatiquement après la première utilisation |
| Planning | Configurer les plages horaires autorisées |
| Supprimer | Suppression définitive |

---

## Planning horaire

Cliquer l'icône calendrier d'un badge pour configurer ses plages d'accès.

- Planning **global** — s'applique à tous les lecteurs
- 7 jours configurables (lundi → dimanche)
- Par jour : activer / désactiver + heure de début + heure de fin
- Par défaut : accès **H24**
- Bouton **Réinitialiser (H24)** pour supprimer toutes les restrictions

Un accès hors planning est enregistré dans l'historique avec le résultat **Planning**.

---

## Historique des accès

Accessible via le bouton **Historique** sur la page principale.

- Affiche les 500 dernières entrées, tous lecteurs confondus
- Résultats : **Autorisé** (vert), **Refusé** (rouge), **Planning** (orange)
- Bouton **Purger** pour vider l'historique

---

## Protection du lecteur

### Principe

Chaque lecteur maintient un compteur d'erreurs indépendant. Une erreur est comptée à chaque badge inconnu ou code PIN invalide présenté.

Lorsque le seuil est atteint, le lecteur est verrouillé automatiquement.

### Trois statuts par lecteur

| Statut | Couleur | Déclencheur | Récupération |
|---|---|---|---|
| **Actif** | Vert | — | — |
| **Bloqué (compteur)** | Orange | Seuil d'erreurs atteint | Automatique sur TAMPER_OFF ou bouton **Réinit.** |
| **Bloqué (manuel)** | Rouge | Bouton **Verrouiller tout** | Bouton **RAZ alarme** ou **Réinit.** individuel |

### Tuile de statut (dashboard)

- Statut coloré (Actif / Bloqué)
- Compteur d'erreurs et seuil (ex. 2 / 3)
- Bouton **Réinit.** — remet le compteur à 0 et déverrouille si bloqué

### Panneau de sécurité (page principale)

- Verrouiller / Déverrouiller tous les lecteurs en une fois
- Remettre à 0 tous les compteurs d'erreur

---

## Commandes matérielles avancées (lecteur physique)

Accessibles depuis l'onglet **Configuration** de chaque lecteur physique.

| Commande | Description |
|---|---|
| **DELAY_LOCKOUT** | Durée de blocage du clavier après erreurs (1–255 min) — persistée en EEPROM |
| **UNLOCK_CODE** | Code de déverrouillage d'urgence à 10 chiffres — persisté en EEPROM |
| **Tester CODE_OK / CODE_KO** | Envoie le signal manuellement (test câblage) |
| **Tester LOCK / LOCK_UNTIL / UNLOCK** | Verrouillage / déverrouillage manuel pour test |

---

## FAQ

**Je n'ai pas de matériel — puis-je tester le plugin ?**  
Oui. Un **Lecteur démo** (virtuel) est créé automatiquement à l'installation. Il permet de tester badge, PIN, planning, historique, verrouillage sans aucun équipement physique.

**Mon matériel n'est pas un lecteur PTVO Zigbee — le plugin fonctionne-t-il ?**  
Oui. Le plugin fonctionne avec tout équipement Jeedom capable d'envoyer les événements `BADGE:xxx`, `PIN:xxx`, `ABANDON`, `TAMPER_ON`, `TAMPER_OFF` sur une commande info. Le protocole (Z-Wave, WiFi, MQTT…) est indifférent.

**Mon équipement envoie les données dans un format différent — que faire ?**  
Utiliser le mode **Virtuel — Entrées externes**. Créez un lecteur en mode "Entrées externes", puis écrivez un scénario Jeedom qui lit les données de votre équipement, les formate si nécessaire, et appelle la commande `Envoyer un badge` ou `Envoyer un PIN` du lecteur. Le plugin prend en charge tout le reste.

**Le converter z2m ne se charge pas**  
Vérifier que le plugin z2m est installé dans `/var/www/html/plugins/z2m`, activer le plugin wiegandAccess, puis redémarrer Zigbee2MQTT.

**La détection automatique PTVO ne se déclenche pas**  
L'équipement z2m sélectionné doit avoir été reconnu avec le modèle `ptvo_wiegand`. Si le lecteur était appairé avant l'installation du converter, le ré-interviewer depuis z2m.

**Un badge est refusé alors qu'il devrait être autorisé**  
Vérifier : le badge est-il actif ? Un planning horaire restreint-il l'accès ? Le lecteur est-il verrouillé (tuile orange ou rouge) ?

**Un lecteur reste bloqué après la fin du timer**  
Si le signal TAMPER_OFF n'a pas été reçu (liaison instable), utiliser le bouton **Réinit.** sur la tuile du lecteur.

**Les données ont disparu après une désactivation**  
Désactiver puis réactiver le plugin conserve toutes les données. Seule la suppression depuis le market efface les données.

---

## Protocole événements (référence)

| Direction | Message | Description |
|---|---|---|
| Lecteur → Jeedom | `BADGE:XXXXXXXX` | Badge lu |
| Lecteur → Jeedom | `PIN:XXXXXX` | Code PIN saisi |
| Lecteur → Jeedom | `ABANDON` | Saisie abandonnée |
| Lecteur → Jeedom | `TAMPER_ON` | Anti-arrachement / boîtier ouvert |
| Lecteur → Jeedom | `TAMPER_OFF` | Boîtier refermé |
| Jeedom → Lecteur | `CODE_OK` | Signal accès autorisé |
| Jeedom → Lecteur | `CODE_KO` | Signal accès refusé |
| Jeedom → Lecteur | `DELAY_LOCKOUT=X` | Durée de blocage en minutes |
| Jeedom → Lecteur | `UNLOCK_CODE=XXXXXXXXXX` | Code de déverrouillage d'urgence |
| Jeedom → Lecteur | `LOCK` | Verrouillage par timer firmware |
| Jeedom → Lecteur | `LOCK_UNTIL` | Verrouillage indéfini |
| Jeedom → Lecteur | `UNLOCK` | Déverrouillage immédiat |

---

*© mickadam29*
