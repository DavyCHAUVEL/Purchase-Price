# RUNBOOK - T-000029 TASK

## Ticket
- Name: T-000029 TASK
- Id: a0NIV00001WB1tw2AD
- Subject: TASK
- Status: In Progress
- LastModifiedDate: 2025-09-05T14:22:21.000+0000

## Workflow Ticket Status
- New: ticket cree, pas encore pris en charge.
- In Progress: analyse/implementation en cours.
- Resolved (sandbox): correction deployee et verifiee en sandbox.
- Closed: valide metier + cloture ticket.

## Resolution
- Date/heure:
- Passage de statut (ex: New -> In Progress):
- Actions exactes realisees (fichiers/flows/classes/pages modifies):
- Commandes sf executees:
- Deploy ID + Status:
- Risques/Remaining:

## Contexte
<p>Hello, </p><p><br></p><p>J&#39;aimerai traiter les task plus facilement car c&#39;est vite tr├⌐s long ├á traiter ! </p><p>J&#39;avais penser ├á ajouter une case &quot;send an email&quot; comme pour les leads avec un email type qui permet de contacter le client rapidement ! </p><p><br></p><p>Ce mail automatique pourrait s&#39;inspirer des mails envoy├⌐s au d├⌐but pour les ├⌐chantillons ! </p><p><br></p><p>Merci bcp ! </p>

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
