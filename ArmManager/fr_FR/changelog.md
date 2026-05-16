# Changelog ArmManager

## v0.2.0 (2026-05-16)

Première version publiée sur le market Jeedom.

### Fonctionnalités

- Liaison à une commande info source (clavier ou télécommande Zigbee via Z2M)
- Mapping des quatre événements standard : `arm_away`, `arm_home`, `disarm`, `sos`
- Action cible : commande Jeedom ou scénario
- Détection automatique SOS via commande `sos_alarm` de l'équipement source
- Compatible DAEWOO WKE502Z nativement
- Compatible tout appareil exposant les valeurs standard via EventTranslator
- Listener unique par équipement, reconstruit automatiquement à la sauvegarde
- Requiert Jeedom 4.4 minimum
