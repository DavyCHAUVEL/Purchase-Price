# RUNBOOK - T-000036 Eviter paiement envois ├⌐chant sur certaines zones

## Ticket
- Name: T-000036 Eviter paiement envois ├⌐chant sur certaines zones
- Id: a0NIV00001WbcrN2AR
- Subject: Eviter paiement envois ├⌐chant sur certaines zones
- Status: In Progress
- LastModifiedDate: 2025-09-05T10:35:44.000+0000

## Workflow Ticket Status
- New: ticket cree, pas encore pris en charge.
- In Progress: analyse/implementation en cours.
- Resolved (sandbox): correction deployee et verifiee en sandbox.
- Closed: valide metier + cloture ticket.

## Resolution
- Format a renseigner a chaque avancee:
  - Date/heure
  - Passage de statut (ex: `New -> In Progress`, `In Progress -> Resolved (sandbox)`)
  - Actions exactes realisees (fichier/flow/page/classes modifies)
  - Commandes `sf` executees
  - Resultat de deploiement (Deploy ID + Status)
  - Risques/restants

## Contexte
<p>faire apparaitre un pop-up ou blocage afin qu&#39;on soit averti et qu&#39;on n&#39;oublie pas ceci : </p><p><br></p><p>pour tous les autres pays que  (France, Europe , UK , Suisse et Maroc)┬á┬á: on fait payer le transport au client =&gt; lui demander de faire enlever la marchandise par son transporteur pour ne pas avoir de frais.</p><p><br></p><p>merci</p>

## Low-Code Analysis (obligatoire)
- Validation Rule:
- Formula Field:
- Record-Triggered Flow:
- Scheduled Flow:

## Flow Limitation (si Apex requis)
- Raison technique precise:

## Plan implementation
1. Retrieve metadata cible
2. Implement declarative first
3. Validate deploy
4. Deploy

## Commandes standard (PROD)
```bash
sf org display --target-org iwase-prod
sf project retrieve start --target-org iwase-prod --metadata CustomObject:Purchase_Price__c
sf project deploy validate --target-org iwase-prod --source-dir force-app
sf project deploy start --target-org iwase-prod --source-dir force-app
```

## Commandes standard (Sandbox PartialSB)
```bash
sf org display --target-org ma-sandbox
sf project deploy validate --target-org ma-sandbox --source-dir force-app
sf project deploy start --target-org ma-sandbox --source-dir force-app
```
