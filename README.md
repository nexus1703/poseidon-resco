RESCO – SPEC MECATRONIQUE v1
Station de Diagnostic Automatisée – Version 1.0
1. Objectif

RESCO est une station mécatronique automatisée permettant :

Diagnostic matériel standardisé

Conditions de test strictement reproductibles

Suppression de la variabilité humaine

Exécution autonome des séquences SHORT et FULL

Production de données exploitables par Poseidon (Trident)

La station doit fonctionner sans intervention technicien durant le test.

2. Architecture Générale
2.1 Sous-systèmes principaux

Module de mouvement (rotule 3 axes)

Module de maintien appareil

Module optique (caméras + mire)

Module audio (micro + haut-parleur)

Module RF (WiFi / Bluetooth)

Module USB / alimentation / mesure électrique

Module actionneurs boutons & tactile

Système de capot verrouillable

Module sécurité & arrêt d’urgence

Unité de contrôle station (Mini PC + MCU)

3. Calibrage Initial (Étape obligatoire)
3.1 Objectif

Garantir que le référentiel mécanique et optique est exact avant chaque session de test.

3.2 Procédure

Avant tout test appareil :

Vérification auto capteurs internes

Mise en position zéro des axes yaw/pitch/roll

Analyse mire de calibrage via caméra

Vérification alignement laser ou pattern optique

Validation stabilité luminosité mire

Validation calibration caméra haute/basse

3.3 Règle

Si calibrage = FAIL
→ Aucun test appareil autorisé
→ État station = CALIBRATION_REQUIRED

Calibrage validé = horodaté et associé à station_id.

4. Module Mouvement – Rotule 3 Axes
4.1 Axes contrôlés

Yaw

Pitch

Roll

4.2 Exigences

Position zéro reproductible

Séquences motorisées programmables

Mouvements lents et contrôlés

Détection fin de course

Tolérance angulaire définie

4.3 Utilisation

Test IMU (gyro / acceleromètre)

Détection faux contact USB

Test instabilité sous mouvement

5. Module Maintien Appareil
5.1 Plateau réglable

Ajustement manuel largeur/longueur

Butées mécaniques référencées

Support ESD

Maintien constant (pression calibrée)

5.2 Exigence

Position appareil reproductible entre sessions.

6. Module Optique
6.1 Caméra supérieure

Vérification position appareil

Lecture écran device

Détection anomalies visuelles grossières

6.2 Caméra inférieure

Test caméra device (via mire affichée)

Détection flou / artefacts

6.3 Mire de référence

Luminosité stable

Pattern fixe

Utilisée pour :

calibration station

test optique

7. Module Audio

Haut-parleur station

Micro station

Permet :

Test speaker device

Test micro device

Mesure amplitude et cohérence signal

Le capot fermé garantit stabilité acoustique.

8. Module RF
8.1 WiFi station

SSID dédié

Environnement contrôlé

Test connexion + stabilité

8.2 Bluetooth station

Module pairing automatique

Test échange data simple

8.3 Réseau mobile (v1)

Test détection SIM

Test présence modem

Lecture niveau signal

Pas de simulation cellulaire active v1

9. Module USB & Mesure Électrique

Dock principal USB-C

Adaptateurs interchangeables

Mesure tension

Mesure courant

Test stabilité charge

Test énumération data

Séquence de micro-mouvement possible pendant charge pour détection instabilité.

10. Module Actionneurs Boutons
10.1 Actionneurs linéaires calibrés

Pression constante

Course contrôlée

Profils de pression configurables

10.2 Actions supportées

Power

Volume +

Volume –

Combinaisons (Power + Vol-, etc.)

10.3 Objectif

Mode maintenance

Bootloader

Vérification réponse hardware

11. Module Test Tactile (v1)

Points d’appui calibrés (zones critiques)

Pression constante

Détection réponse via software injecté

Extension future possible vers portique XY complet.

12. Capot & Verrouillage
12.1 Fonction

Isolation RF

Isolation acoustique

Sécurité opérateur

Stabilisation environnement test

12.2 Règles

Capot fermé → Lock mécanique activé
Référentiel snapshot
Aucune interaction manuelle autorisée

Unlock uniquement :

Fin test normal

Ou procédure sécurité

13. Sécurité
13.1 Arrêt d’urgence (E-STOP)

Bouton physique accessible

Coupe moteurs

Coupe alimentation USB

Stop actionneurs

Log événement

13.2 Surveillance interne

Capteur température

(Option) capteur fumée

Seuil sécurité configurable

Déclenchement :

SAFE_STOP

Journalisation

Alerte opérateur

14. Séquence Globale Test

Self-check station

Calibrage référentiel

Placement appareil

Dock USB

Capot fermé & lock

Séquence SHORT ou FULL

Génération rapport structuré

Push événement vers Poseidon

Unlock fin de test

15. Exigences Industrielles

Reproductibilité des conditions

Journalisation complète

Append-only logs station

Identification station_id

Identification version firmware station

16. État Station

IDLE

CALIBRATION_REQUIRED

READY

TEST_RUNNING

SAFE_STOP

ERROR
