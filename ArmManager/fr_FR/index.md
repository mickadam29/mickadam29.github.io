# ArmManager — Documentation

## Présentation

ArmManager centralise le pilotage du plugin **Alarme** natif de Jeedom depuis vos claviers et télécommandes Zigbee. Il respecte le standard **IAS ACE** (cluster Zigbee 0x0501) exposé par Zigbee2MQTT.

> **Prérequis :** le plugin **z2m** (Zigbee2MQTT officiel Jeedom) doit être installé et actif.

Chaque événement reçu déclenche la commande ou le scénario de votre choix.

**Appareils compatibles nativement** (standard IAS ACE Z2M)
- **LDESENK09** : télécommande Zigbee (cluster IAS ACE natif)
- Tout équipement exposant les valeurs IAS ACE standard via Zigbee2MQTT

**Appareils compatibles via [EventTranslator](https://mickadam29.github.io/EventTranslator/fr_FR/)**
- **DAEWOO WKE502Z** et tout appareil utilisant un vocabulaire non conforme IAS ACE
- Tout appareil dont les valeurs envoyées ne correspondent pas aux 6 événements IAS ACE standard

---

## Installation

1. Depuis le market Jeedom, installer et activer le plugin **z2m**
2. Depuis le market Jeedom, rechercher **ArmManager**
3. Installer le plugin
4. Activer le plugin dans **Plugins > Gestion des plugins**

Aucune dépendance requise.

> **Optionnel :** si votre appareil n'est pas nativement compatible IAS ACE (badge orange à la découverte), installez également le plugin **[EventTranslator](https://mickadam29.github.io/EventTranslator/fr_FR/)** pour adapter ses valeurs.

---

## Découverte automatique

Le bouton **Découverte** analyse les équipements Zigbee présents dans z2m et identifie les candidats compatibles :

- **IAS ACE natif** (badge vert) : l'appareil possède le cluster `ssIasAce` — brancheable directement sur ArmManager
- **Compatible probable** (badge orange) : l'appareil expose des valeurs arm-related sans cluster IAS ACE natif — passer par [EventTranslator](https://mickadam29.github.io/EventTranslator/fr_FR/) pour adapter les valeurs

Cliquer sur **Utiliser** crée automatiquement un équipement ArmManager pré-configuré avec la commande source. Les appareils déjà configurés sont exclus des résultats.

---

## Configuration

### Étape 1 — Ajouter un équipement

Ouvrir **Plugins > Sécurité > ArmManager**, puis cliquer sur **Découverte** ou **Ajouter un clavier**.

Renseigner :
- **Nom** : nom de l'équipement dans Jeedom
- **Objet parent** : objet Jeedom auquel rattacher l'équipement
- **Type d'appareil** : Clavier (icône clavier) ou Télécommande / keyfob (icône bouclier) — affecte l'icône dans la liste
- **Activer / Visible**

### Étape 2 — Liaison Zigbee2MQTT

Dans la section **Liaison Zigbee2MQTT**, sélectionner la **commande source** : c'est la commande `action` exposée par l'équipement Z2M (ou la commande traduite par EventTranslator).

Un indicateur s'affiche sous le champ :
- **Dernière valeur reçue** : mise à jour en temps réel dès qu'une touche est pressée
- **Badge vert — IAS ACE natif** : la valeur est conforme au standard, ArmManager la traite directement
- **Badge orange — Nécessite EventTranslator** : la valeur n'est pas conforme IAS ACE, il faut passer par EventTranslator pour la traduire
- **Badge gris — Aucune valeur reçue** : aucun événement reçu depuis le démarrage de Jeedom

Le bouton **↺** force un rafraîchissement manuel de la valeur.

**Tamper (optionnel)** : si l'équipement source expose une commande info binaire de sabotage (ex : `tamper`), sélectionnez-la dans le champ **Tamper**. Dès qu'elle passe à `1`, l'action **Tamper** configurée dans le tableau ci-dessous est déclenchée. Laissez ce champ vide si l'appareil ne remonte pas de signal tamper.

### Étape 3 — Actions du clavier / télécommande

Pour chacun des sept événements, choisir le type d'action et la cible :

| Événement | Valeur | Description |
|---|---|---|
| **Armement total** | `arm_all_zones` | Toutes les zones actives |
| **Armement jour** | `arm_day_zones` | Zones périmètre (portes/fenêtres) |
| **Armement nuit** | `arm_night_zones` | Zones intérieures (détecteurs de mouvement) |
| **Désarmement** | `disarm` | Désactive l'alarme |
| **Panique** | `panic` | Alerte panique |
| **Urgence** | `emergency` | Alerte urgence |
| **Tamper** | `tamper` | Sabotage détecté sur l'appareil (optionnel) |

Pour chaque événement :
- **Aucune action** : événement ignoré
- **Commande** : exécute une commande action Jeedom (ex : `Alarme → Mode Toutes Zones`)
- **Scénario** : lance un scénario Jeedom (ex : sirène + notification)

### Étape 4 — Importer une configuration (optionnel)

Si vous possédez plusieurs appareils identiques, configurez le premier puis utilisez le bouton **Importer une configuration** (en haut à droite de la section Actions) sur les suivants. La configuration des 6 actions est copiée en un clic depuis n'importe quel autre équipement ArmManager déjà configuré.

### Étape 5 — Sauvegarder

Cliquer sur **Sauvegarder**. Le listener est reconstruit automatiquement.

---

## Détection panique automatique

Si l'équipement source expose une commande info `sos_alarm` (commande binaire indiquant une alarme active), ArmManager la surveille en permanence. Dès qu'elle passe à `1`, l'action `panic` est déclenchée **quelle que soit la valeur reçue** sur la commande principale.

## Détection tamper (sabotage)

Si une commande tamper est configurée dans le champ **Tamper** de la section "Liaison Zigbee2MQTT", ArmManager surveille cette commande indépendamment des événements IAS ACE. Dès qu'elle passe à `1`, l'action **Tamper** du tableau est exécutée. Ce champ est entièrement optionnel : si l'appareil ne remonte pas de signal tamper ou si aucune action n'est souhaitée, laissez-le vide.

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

## Utiliser avec EventTranslator

Certains appareils Zigbee n'exposent pas les valeurs standard IAS ACE. Le plugin **[EventTranslator](https://mickadam29.github.io/EventTranslator/fr_FR/)** permet de traduire en temps réel les valeurs envoyées par l'appareil vers le vocabulaire IAS ACE attendu par ArmManager.

**Workflow :**

1. Dans **EventTranslator**, créer un équipement avec la commande `action` de l'appareil Z2M comme source
2. Ajouter les règles de traduction : valeur brute → valeur IAS ACE cible
3. Dans **ArmManager**, sélectionner la commande **traduite** (sortie d'EventTranslator) comme source — et non la commande brute Z2M

La commande source d'ArmManager doit toujours exposer des valeurs IAS ACE standard. Si elle affiche un badge orange, c'est que les valeurs reçues ne sont pas encore traduites.

---

## Exemple concret — DAEWOO WKE502Z via EventTranslator

Le WKE502Z expose un vocabulaire non conforme IAS ACE (`arm_away`, `arm_home`, `sos`). Configurer **[EventTranslator](https://mickadam29.github.io/EventTranslator/fr_FR/)** pour traduire les valeurs :

| Valeur WKE502Z | Valeur traduite (IAS ACE) |
|---|---|
| `arm_away` | `arm_all_zones` |
| `arm_home` | `arm_day_zones` |
| `disarm` | `disarm` |
| `sos` | `panic` |

Sélectionner ensuite la commande traduite par EventTranslator comme source dans ArmManager. Le badge passe au vert dès qu'une touche est pressée et que la valeur traduite est conforme.

---

## FAQ

**Puis-je utiliser plusieurs claviers/télécommandes simultanément ?**
Oui, chaque équipement ArmManager est indépendant. Créez un équipement par appareil physique. Utilisez le bouton **Importer une configuration** pour dupliquer rapidement la configuration d'actions.

**Que se passe-t-il si aucune action n'est configurée pour un événement ?**
L'événement est simplement ignoré, aucune erreur n'est générée.

**Le plugin fonctionne-t-il sans le plugin Alarme Jeedom ?**
Oui. Vous pouvez associer n'importe quelle commande action ou scénario, pas uniquement ceux du plugin Alarme.

**Mon appareil expose `arm_night_zones` — comment l'utiliser ?**
Configurez l'action souhaitée dans le champ **Armement nuit**. Si votre plugin Alarme ne dispose pas d'un mode nuit, vous pouvez le lier à un scénario dédié.

**Le badge reste orange même après configuration dans EventTranslator — est-ce normal ?**
Oui. Le badge indique la valeur brute reçue par la commande source. Si vous utilisez EventTranslator, la commande source d'ArmManager doit pointer sur la commande **traduite** (sortie d'EventTranslator), pas sur la commande brute Z2M.

**Le plugin fonctionne-t-il avec un autre protocole que Zigbee (Z-Wave, Matter...) ?**
Pas officiellement pour le moment. ArmManager est conçu autour du standard IAS ACE (Zigbee). Si vous avez un besoin concret sur un autre protocole, faites-le savoir — une évolution pourra être envisagée.
