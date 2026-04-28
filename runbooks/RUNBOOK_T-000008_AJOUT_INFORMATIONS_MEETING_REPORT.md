# RUNBOOK - T-000008 AJOUT INFORMATIONS MEETING REPORT

## Ticket
- Name: T-000008 AJOUT INFORMATIONS MEETING REPORT
- Id: a0NIV00001UcGra2AF
- Subject: AJOUT INFORMATIONS MEETING REPORT
- Status: In Progress
- LastModifiedDate: 2025-09-19T14:09:44.000+0000

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
<p>Hello, </p><p>J&#39;en profite pour faire remonter un de mes besoins. </p><p>Je souhaiterai pouvoir sortir des reports de meeting report deux informations. </p><p>Les doubles sourcing des clients (nom INCI + volume) + les innovations/mati├¿res recherch├⌐s (INCI)</p><p>Proposition ajouter deux encarts dans sales force avec ces deux noms pour pouvoir ensuite faire des extractions et avoir toutes les informations n├⌐cessaires !</p><p>MERCI </p>

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
