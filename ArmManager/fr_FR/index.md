# ArmManager — Documentation

## Présentation

ArmManager centralise le pilotage du plugin **Alarme** natif de Jeedom depuis vos claviers et télécommandes Zigbee. Chaque événement reçu (`arm_away`, `arm_home`, `disarm`, `sos`) déclenche la commande ou le scénario de votre choix.

**Appareils compatibles nativement**
- **DAEWOO WKE502Z** : clavier Zigbee (via Zigbee2MQTT + converter externe)
- Tout équipement exposant les valeurs `arm_away`, `arm_home`, `disarm`, `sos`

**Appareils compatibles via EventTranslator**
- **LDESENK09** et tout appareil utilisant un vocabulaire différent (cluster IAS Z2M…)

---

## Installation

1. Depuis le market Jeedom, rechercher **ArmManager**
2. Installer le plugin
3. Activer le plugin dans **Plugins > Gestion des plugins**

Aucune dépendance requise.

---

## Configuration

### Étape 1 — Ajouter un équipement

Ouvrir **Plugins > Sécurité > ArmManager**, puis cliquer sur **Ajouter un clavier**.

Renseigner :
- **Nom** : nom de l'équipement dans Jeedom
- **Objet parent** : objet Jeedom auquel rattacher l'équipement
- **Activer / Visible**

### Étape 2 — Liaison Zigbee2MQTT

Dans la section **Liaison Zigbee2MQTT**, sélectionner la **commande info du clavier** : c'est la commande `button` ou `action` exposée par l'équipement Z2M (ou la commande traduite par EventTranslator).

### Étape 3 — Actions du clavier

Pour chacun des quatre événements, choisir le type d'action et la cible :

| Événement | Description |
|---|---|
| **Armement total** (`arm_away`) | Toutes les zones actives |
| **Armement partiel** (`arm_home`) | Zone ouvrants uniquement |
| **Désarmement** (`disarm`) | Désactive l'alarme |
| **SOS** (`sos`) | Alerte d'urgence |

Pour chaque événement :
- **Aucune action** : événement ignoré
- **Commande** : exécute une commande action Jeedom (ex : `Alarme → Mode Toutes Zones`)
- **Scénario** : lance un scénario Jeedom (ex : appel d'urgence)

### Étape 4 — Sauvegarder

Cliquer sur **Sauvegarder**. Le listener est reconstruit automatiquement.

---

## Détection SOS automatique

Si l'équipement source expose une commande info `sos_alarm` (commande binaire indiquant une alarme active), ArmManager la surveille en permanence. Dès qu'elle passe à `1`, l'action `sos` est déclenchée **quelle que soit la valeur reçue** sur la commande principale.

---

## Exemple concret — DAEWOO WKE502Z + plugin Alarme

| Bouton clavier | Événement | Action configurée |
|---|---|---|
| Armement total | `arm_away` | `Alarme Domicile → Mode Toutes Zones` |
| Armement partiel | `arm_home` | `Alarme Domicile → Mode Zone Ouvrants` |
| Désarmement | `disarm` | `Alarme Domicile → Désactiver` |
| SOS | `sos` | Scénario `Appel Secours et Aidant` |

---

## Exemple concret — LDESENK09 via EventTranslator

La télécommande LDESENK09 expose des valeurs IAS (`arm_all_zones`, `arm_day_zones`, `disarm`, `panic`) incompatibles avec ArmManager. Utiliser **EventTranslator** pour les traduire :

| Valeur LDESENK09 | Valeur traduite |
|---|---|
| `arm_all_zones` | `arm_away` |
| `arm_day_zones` | `arm_home` |
| `disarm` | `disarm` |
| `panic` | `sos` |

Sélectionner ensuite la commande traduite par EventTranslator comme source dans ArmManager.

---

## FAQ

**Puis-je utiliser plusieurs claviers simultanément ?**
Oui, chaque équipement ArmManager est indépendant. Créez un équipement par appareil physique.

**Que se passe-t-il si aucune action n'est configurée pour un événement ?**
L'événement est simplement ignoré, aucune erreur n'est générée.

**Le plugin fonctionne-t-il sans le plugin Alarme Jeedom ?**
Oui. Vous pouvez associer n'importe quelle commande action ou scénario, pas uniquement ceux du plugin Alarme.
