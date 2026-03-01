Diagramme d’interaction RESCO ↔ Trident

Acteurs :

Opérateur / Tech (déclenchement)

Station RESCO (orchestrateur de test)

Software injecté (sur l’appareil)

Trident API (source de vérité)

Séquence (DIAG – passage Calypso)

Calypso crée Case + Asset

Trident crée WorkOrder DIAG (ou transition vers INTAKE)

L’opérateur pose l’appareil sur RESCO et scanne le QR (asset_id)

RESCO appelle Trident pour “réserver” un run de test

RESCO lance le test SHORT/FULL

RESCO pousse le résultat à Trident

Trident valide le schéma, journalise l’event, applique transition DIAG_COMPLETED

Trident renvoie ACK + nouvel état courant

Mermaid voit instantanément l’état et le rapport

Séquence (QA – passage avant Ready)

Nereus termine la réparation (REPAIR_COMPLETED)

Trident crée/attend WorkOrder QA

L’opérateur lance RESCO en mode QA

RESCO récupère la baseline DIAG depuis Trident

RESCO exécute la séquence QA (SHORT/FULL QA)

RESCO pousse résultat QA

Trident applique QA_VALIDATED ou QA_REJECTED

Si VALIDATED → READY

Si REJECTED → retour IN_REPAIR + défauts attachés

Diagramme UML (Mermaid sequence diagram)

<img width="3980" height="2522" alt="mermaid-diagram" src="https://github.com/user-attachments/assets/88e281c5-77b6-435f-824f-dc7f2cb77cf5" />


Endpoints Trident (v1 – REST)

Créer un run RESCO

POST /rescotests/runs
Body :

asset_id

case_id (optionnel mais utile)

mode = DIAG|QA

test_type = SHORT|FULL

station_id
Retour :

run_id

baseline_ref (si mode QA, référence au DIAG baseline)

expected_schema_version

Pousser le résultat RESCO

POST /rescotests/runs/{run_id}/result
Body :

schema_version

result

confidence

faults[]

metrics{}

evidence_refs[]

Retour :

ack=true

new_state

trident_event_id

Récupérer baseline (optionnel si déjà renvoyé à la création)

GET /assets/{asset_id}/baseline/diag

Règles Trident (v1 – intégration)

Si mode=DIAG et résultat reçu → transition vers DIAG_COMPLETED

Si mode=QA :

si résultat PASS/CONFIRMED_OK → QA_VALIDATED puis READY

sinon → QA_REJECTED puis retour IN_REPAIR (et attacher faults)

Tous les résultats RESCO sont stockés append-only :

event log Trident

lien vers rapport complet (blob store plus tard, fichier local v1)
