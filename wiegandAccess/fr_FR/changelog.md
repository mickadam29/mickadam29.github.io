# Changelog — Wiegand Access

## [1.3.0] — 2026-05-19

### Mode Virtuel — Entrées externes

- **Nouveau mode d'interface** : "Virtuel (entrées externes — scénario, virtuel…)" — le lecteur expose cinq commandes action Jeedom appelables depuis n'importe quel scénario, le plugin Virtuel ou tout autre plugin Jeedom
- **Commandes créées automatiquement** : Envoyer un badge, Envoyer un PIN, Envoyer Abandon, Envoyer Tamper ON, Envoyer Tamper OFF
- Toute la logique du plugin s'applique exactement comme pour un lecteur physique : planning horaire, historique, compteur d'erreurs, verrouillage, actions associées aux badges
- **Cas d'usage** : intégrer un équipement dont le format d'événement est non standard — un scénario traduit les données et appelle les commandes du lecteur

---

## [1.2.5] — 2026-05-17

### Lecteur virtuel de démonstration

- **Lecteur virtuel intégré** : nouveau mode d'interface "Virtuel (démo — sans matériel)" — toute la logique du plugin active sans aucun équipement physique
- **Simulateur intégré** : boutons pour déclencher badge, code PIN, abandon, verrouillage/déverrouillage lecteur directement depuis l'interface
- **Feedback immédiat** : résultat affiché après chaque simulation (Autorisé, Refusé, Planning, Badge enregistré…)
- **Création automatique** : un "Lecteur démo" est créé automatiquement à l'installation/mise à jour du plugin — disponible immédiatement sans configuration
- **Positionnement protocole-agnostique** : le plugin fonctionne avec tout équipement Jeedom capable d'envoyer les événements attendus (Zigbee/PTVO, Z-Wave, WiFi, MQTT ou autre)
- **Correction global_alarm** : le formulaire de lecteur est masqué sur l'équipement "Sécurité wiegandAccess"

---

## [1.2.4] — 2026-05-17

### Import / Export CSV

- **Import CSV** : import en masse de badges depuis un fichier CSV (code, type, label) — prévisualisation avant confirmation, idempotent (libellé existant conservé)
- **Export CSV** : téléchargement du fichier `wiegandAccess_badges_YYYY-MM-DD.csv` vers le PC — format identique à l'import, utilisable comme sauvegarde

---

## [1.2.3] — 2026-05-17

### Enregistrement manuel et correction ordre des octets

- **Enregistrement manuel** : bouton "Enregistrement manuel" dans la section inscription — saisie directe du numéro décimal (badge RFID ou code PIN) sans passer par un lecteur
- **Alignement ACR122U** : ordre des octets du badge_number corrigé dans le converter — le numéro affiché dans Jeedom correspond maintenant exactement à l'UID lu par un ACR122U ou `nfc-list`
- **Correction listener orphelin** : suppression du listener résiduel après remplacement d'un lecteur

---

## [1.2.2] — 2026-05-16

### Inscription double scan et corrections interface

- **Inscription double scan** : séquence bip validée — 1er scan → bip KO immédiat, attendre le bip OK (~5 s, signal lecteur), 2e scan → bip OK + liste badges ouverte avec surbrillance jaune
- **Message de confirmation aligné** : l'indicateur "Badge reçu — présentez à nouveau" (↩) apparaît simultanément au bip OK, plus au moment du 1er scan
- **Cellules action** : type et commande/scénario sur la même ligne (layout flex), bouton loupe pour la sélection — suppression des liens "Sélectionner"
- **Tuile dashboard** : la position de la tuile est désormais préservée lors des mises à jour du plugin (correction `install.php`)
- **Sélecteur emplacement** : le sélecteur de placement de la tuile est correctement initialisé après navigation AJAX

---

## [1.2.1] — 2026-05-14

### Corrections documentation et tuile

- Tuile lecteur : couleurs corrigées — Orange pour verrouillage compteur (automatique), Rouge pour verrouillage manuel
- Documentation : badges globaux et numéros Wiegand par matériel, section "Protection du lecteur", panneau de sécurité simplifié, mention interface WIEGAND ZIGBEE

---

## [1.2.0] — 2026-05-14

### Configuration simplifiée — Interfaces WIEGAND ZIGBEE

- **Détection automatique** : lors du choix de la commande `event`, le plugin détecte l'interface et remplit automatiquement toutes les commandes (CODE_OK, CODE_KO, DELAY_LOCKOUT, UNLOCK_CODE, LOCK, LOCK_UNTIL, UNLOCK, badge_number, pin_number)
- **Valeurs par défaut** appliquées à la création : seuil d'erreurs à **3**, délai DELAY_LOCKOUT à **15 min**, code UNLOCK_CODE **aléatoire** (10 chiffres, modes PIN)
- **Bouton Générer** : produit un code UNLOCK_CODE aléatoire valide à tout moment (premier chiffre non nul, sans répétition consécutive)
- **Formulaire progressif** : les commandes du lecteur n'apparaissent qu'après avoir choisi le type et la commande `event`

### Corrections

- Après une sauvegarde, le formulaire restait masqué sans rechargement de page (F5 requis)
- Boutons Générer et Envoyer regroupés visuellement, alignés avec la ligne DELAY_LOCKOUT

---

## [1.1.0] — 2026-05-14

### Protection du lecteur (alarme anti-intrusion)

- Compteur d'erreurs indépendant par lecteur
- Seuil d'erreurs configurable individuellement par lecteur (défaut : 3)
- Trois statuts par lecteur : **Actif** (vert), **Bloqué compteur** (orange), **Bloqué manuel** (rouge)
- Au seuil : verrouillage via `LOCK` (timer firmware) — récupération automatique sur `TAMPER_OFF`
- Verrouillage global manuel via `LOCK_UNTIL` (bouton "Verrouiller tout")
- Tuile de statut par lecteur dans le dashboard : statut coloré, compteur erreurs/seuil, bouton Réinit.
- Bouton Réinit. individuel : remet le compteur à 0 et déverrouille si bloqué par compteur

### Commandes matérielles de verrouillage

- Nouvelles commandes par lecteur : `LOCK` (timer), `LOCK_UNTIL` (indéfini), `UNLOCK`
- Boutons de test dans la configuration de chaque lecteur

### Corrections

- Désactivation du plugin : les données et équipements sont désormais conservés (seule la suppression depuis le market efface les données)
- Tuile lecteur : le statut se met à jour automatiquement sans rechargement de page

---

## [1.0.0] — 2026-05-12

### Première version stable

- Enregistrement global de badges et codes PIN sur tous les lecteurs simultanément
- Codes globaux communs à tous les lecteurs
- Planning horaire global par badge (7 jours, plages horaires configurables)
- Usage unique : suppression automatique après utilisation
- Historique des accès (500 entrées — accordé / refusé / hors-planning)
- Actions par badge : commande Jeedom ou scénario
- Événements lecteur : Tamper ON/OFF, Abandon
- Commandes UART avancées : DELAY_LOCKOUT, UNLOCK_CODE
- Déploiement automatique du converter pour Zigbee2MQTT
- Interface en français, navigation inline sans rechargement de page
