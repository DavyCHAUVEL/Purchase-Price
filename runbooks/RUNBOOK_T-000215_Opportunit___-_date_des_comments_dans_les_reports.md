# RUNBOOK - T-000215 Opportunit├⌐ - date des comments dans les reports

## Ticket
- Name: T-000215 Opportunit├⌐ - date des comments dans les reports
- Id: a0NIV00001oxtHh2AI
- Subject: Opportunit├⌐ - date des comments dans les reports
- Status: In Progress
- LastModifiedDate: 2026-04-10T15:37:12.000+0000

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
<p>Nous faisons des reports &quot;Opportunities with actvities&quot; pour pouvoir faire ressortir les commentaires ajout├⌐s dans les opportunit├⌐s. Cependant j&#39;ai remarqu├⌐ qu&#39;aucun commentaire datant d&#39;avant 2025 ne ressort.. Ce qui ne permet pas de faire ressortir le suivi complet d&#39;une opportunit├⌐, est-ce qu&#39;il y a un blocage de fait sur les dates ?</p>

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
