# ArmManager — Documentation

## Présentation

ArmManager centralise le pilotage du plugin **Alarme** natif de Jeedom depuis vos claviers et télécommandes Zigbee. Il respecte le standard **IAS ACE** (cluster Zigbee 0x0501) exposé par Zigbee2MQTT.

> **Prérequis :** le plugin **z2m** (Zigbee2MQTT officiel Jeedom) doit être installé et actif.

Chaque événement reçu déclenche la commande ou le scénario de votre choix.

**Appareils compatibles nativement** (standard IAS ACE Z2M)
- **LDESENK09** : télécommande Zigbee (cluster IAS ACE natif)
- Tout équipement exposant les valeurs IAS ACE standard via Zigbee2MQTT

**Appareils compatibles via EventTranslator**
- **DAEWOO WKE502Z** et tout appareil utilisant un vocabulaire non conforme IAS ACE

---

## Installation

1. Depuis le market Jeedom, installer et activer le plugin **z2m**
2. Depuis le market Jeedom, rechercher **ArmManager**
2. Installer le plugin
3. Activer le plugin dans **Plugins > Gestion des plugins**

Aucune dépendance requise.

---

## Découverte automatique

Le bouton **Découverte** analyse les équipements Zigbee présents dans z2m et identifie les candidats compatibles :

- **IAS ACE natif** (badge vert) : l'appareil possède le cluster `ssIasAce` — brancheable directement sur ArmManager
- **Compatible probable** (badge orange) : l'appareil expose des valeurs arm-related sans cluster IAS ACE natif — à utiliser via EventTranslator

Cliquer sur **Utiliser** crée automatiquement un équipement ArmManager pré-configuré avec la commande source. Les appareils déjà configurés sont exclus des résultats.

---

## Configuration

### Étape 1 — Ajouter un équipement

Ouvrir **Plugins > Sécurité > ArmManager**, puis cliquer sur **Découverte** ou **Ajouter un clavier**.

Renseigner :
- **Nom** : nom de l'équipement dans Jeedom
- **Objet parent** : objet Jeedom auquel rattacher l'équipement
- **Activer / Visible**

### Étape 2 — Liaison Zigbee2MQTT

Dans la section **Liaison Zigbee2MQTT**, sélectionner la **commande info du clavier** : c'est la commande `action` exposée par l'équipement Z2M (ou la commande traduite par EventTranslator).

### Étape 3 — Actions du clavier

Pour chacun des six événements IAS ACE, choisir le type d'action et la cible :

| Événement | Valeur Z2M | Description |
|---|---|---|
| **Armement total** | `arm_all_zones` | Toutes les zones actives |
| **Armement jour** | `arm_day_zones` | Zones périmètre (portes/fenêtres) |
| **Armement nuit** | `arm_night_zones` | Zones intérieures (détecteurs de mouvement) |
| **Désarmement** | `disarm` | Désactive l'alarme |
| **Panique** | `panic` | Alerte panique |
| **Urgence** | `emergency` | Alerte urgence |

Pour chaque événement :
- **Aucune action** : événement ignoré
- **Commande** : exécute une commande action Jeedom (ex : `Alarme → Mode Toutes Zones`)
- **Scénario** : lance un scénario Jeedom (ex : appel d'urgence)

### Étape 4 — Sauvegarder

Cliquer sur **Sauvegarder**. Le listener est reconstruit automatiquement.

---

## Détection panique automatique

Si l'équipement source expose une commande info `sos_alarm` (commande binaire indiquant une alarme active), ArmManager la surveille en permanence. Dès qu'elle passe à `1`, l'action `panic` est déclenchée **quelle que soit la valeur reçue** sur la commande principale.

---

## Exemple concret — LDESENK09 + plugin Alarme (natif)

La LDESENK09 est compatible nativement — elle expose directement le standard IAS ACE.

| Bouton télécommande | Événement | Action configurée |
|---|---|---|
| Armement total | `arm_all_zones` | `Alarme Domicile → Mode Toutes Zones` |
| Armement partiel | `arm_day_zones` | `Alarme Domicile → Mode Zone Ouvrants` |
| Désarmement | `disarm` | `Alarme Domicile → Désactiver` |
| SOS | `panic` | Scénario `Appel Secours et Aidant` |

---

## Exemple concret — DAEWOO WKE502Z via EventTranslator

Le WKE502Z expose un vocabulaire non conforme IAS ACE (`arm_away`, `arm_home`, `sos`). Utiliser **EventTranslator** pour le traduire :

| Valeur WKE502Z | Valeur traduite (IAS ACE) |
|---|---|
| `arm_away` | `arm_all_zones` |
| `arm_home` | `arm_day_zones` |
| `disarm` | `disarm` |
| `sos` | `panic` |

Sélectionner ensuite la commande traduite par EventTranslator comme source dans ArmManager.

---

## FAQ

**Puis-je utiliser plusieurs claviers simultanément ?**
Oui, chaque équipement ArmManager est indépendant. Créez un équipement par appareil physique.

**Que se passe-t-il si aucune action n'est configurée pour un événement ?**
L'événement est simplement ignoré, aucune erreur n'est générée.

**Le plugin fonctionne-t-il sans le plugin Alarme Jeedom ?**
Oui. Vous pouvez associer n'importe quelle commande action ou scénario, pas uniquement ceux du plugin Alarme.

**Mon appareil expose `arm_night_zones` — comment l'utiliser ?**
Configurez l'action souhaitée dans le champ **Armement nuit**. Si votre plugin Alarme ne dispose pas d'un mode nuit, vous pouvez le lier à un scénario dédié.
