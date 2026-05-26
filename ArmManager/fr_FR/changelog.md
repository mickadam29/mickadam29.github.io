# Changelog ArmManager

## v1.0.1 (2026-05-26)

### Corrections

- Icône télécommande/keyfob : remplacement de `fa-gamepad` par `fa-shield-alt`

---

## v1.0.0 (2026-05-16)

Première version publiée sur le market Jeedom.

### Fonctionnalités

- Liaison à une commande info source (clavier ou télécommande Zigbee via Z2M ou EventTranslator)
- Support des six événements standard IAS ACE : `arm_all_zones`, `arm_day_zones`, `arm_night_zones`, `disarm`, `panic`, `emergency`
- Action cible par événement : commande Jeedom ou scénario
- Découverte automatique des appareils Zigbee compatibles dans z2m
- Badge de conformité temps réel : vert (IAS ACE natif), orange (nécessite EventTranslator), gris (aucune valeur reçue), rouge (commande source introuvable)
- Import de configuration entre équipements (duplication en un clic des 6 actions)
- Type d'appareil : Clavier ou Télécommande / keyfob (icône différenciée)
- Détection automatique SOS via commande `sos_alarm` de l'équipement source
- Listener unique par équipement, reconstruit automatiquement à la sauvegarde
- Nettoyage automatique du listener à la suppression ou désactivation
- Requiert Jeedom 4.4 minimum et plugin z2m
