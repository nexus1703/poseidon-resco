Diagramme d’interaction RESCO ↔ Trident (v1 – REST “simple et montrable”)

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

Tu peux coller ça tel quel dans un README (oui, c’est le comble 😄) :

sequenceDiagram
  autonumber
  actor Tech as Opérateur
  participant Calypso as Calypso (Intake)
  participant Trident as Trident (State Machine)
  participant Resco as RESCO Station
  participant Inject as Software Injecté (Device)
  participant MermaidUI as Mermaid UI

  %% ---- DIAG PASS (Calypso) ----
  Tech->>Calypso: Create Case + Asset (photos, symptoms)
  Calypso->>Trident: POST /cases/{case_id}/assets/{asset_id}/intake
  Trident-->>Calypso: 201 Created (state=INTAKE)

  Tech->>Resco: Scan QR (asset_id) + Start DIAG (SHORT/FULL)
  Resco->>Trident: POST /rescotests/runs (asset_id, test_type, mode=DIAG)
  Trident-->>Resco: 201 run_id + baseline_ref(optional)
  Resco->>Inject: Start test sequence (USB-injected)
  Inject-->>Resco: metrics + faults + result
  Resco->>Trident: POST /rescotests/runs/{run_id}/result (report JSON)
  Trident-->>Resco: 200 ACK (new_state=DIAG_COMPLETED)
  Trident-->>MermaidUI: State+Timeline available (poll/websocket later)

  %% ---- QA PASS (Pre-Delivery) ----
  Tech->>Resco: Start QA (SHORT/FULL) (asset_id)
  Resco->>Trident: POST /rescotests/runs (asset_id, test_type, mode=QA)
  Trident-->>Resco: 201 run_id + baseline_ref(DIAG report)
  Resco->>Inject: Start QA sequence
  Inject-->>Resco: metrics + faults + result
  Resco->>Trident: POST /rescotests/runs/{run_id}/result (report JSON)
  alt QA PASS
    Trident-->>Resco: 200 ACK (state=QA_VALIDATED -> READY)
  else QA FAIL
    Trident-->>Resco: 200 ACK (state=QA_REJECTED -> IN_REPAIR)
  end

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
