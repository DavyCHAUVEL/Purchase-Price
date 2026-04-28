# RUNBOOK - T-000076 Supprimer les lightning pages Inutiles

## Ticket
- Name: T-000076 Supprimer les lightning pages Inutiles
- Id: a0NIV00001c0ohg2AA
- Subject: Supprimer les lightning pages Inutiles
- Status: In Progress
- LastModifiedDate: 2025-11-07T15:52:49.000+0000

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
<p>Il y a des lightning pages pour les objets Product et Account qui sont en double ou ├á ne pas utiliser. Il faut v├⌐rifier l&#39;utilit├⌐ d&#39;avoir des lightning page en double et supprimer les pages inutiles si besoin pour faciliter la gestion des vues. <strong>A v├⌐rifier avec Antoine avant avant de proc├¿der ├á une action. </strong></p>

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
