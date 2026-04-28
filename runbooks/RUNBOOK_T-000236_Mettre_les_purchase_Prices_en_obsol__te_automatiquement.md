# RUNBOOK - T-000236 Mettre les purchase Prices en obsol├¿te automatiquement

## Ticket
- Name: T-000236 Mettre les purchase Prices en obsol├¿te automatiquement
- Id: a0NIV00001qRGVF2A4
- Subject: Mettre les purchase Prices en obsol├¿te automatiquement
- Status: New
- LastModifiedDate: 2026-04-23T12:58:32.000+0000

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
<p>Bonjour Davy,</p><p>Peux tu faire stp un flow qui s&#39;execute quotidiennement, qui v├⌐rifie s&#39;il y a des prix d&#39;achat avec statut validate et une date de fin de validit├⌐ dans le pass├⌐ et le flow change le statut de prix d&#39;achat autoamtiquement en obsol├¿te.</p><p>Merci!</p>

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
