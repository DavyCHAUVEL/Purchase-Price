# RUNBOOK - T-000072 Amelioration Formulas

## Ticket
- Name: T-000072 Amelioration Formulas
- Id: a0NIV00001aOlyE2AS
- Subject: Amelioration Formulas
- Status: In Progress
- LastModifiedDate: 2025-11-14T14:34:55.000+0000

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
<p>Dans la m├¬me lanc├⌐e que l&#39;am├⌐lioration Products, voici mes id├⌐es pour les champs de Formulas. </p><p><br></p><p>Ajouter des champs URL pour les choses suivantes : </p><ul><li>Videos</li><li>Flyers PDF</li><li>Webinar</li><li>Other</li></ul><p>A chaque fois, la partie Other c&#39;est au cas o├╣ nous cr├⌐ions un nouveau type de contenu.</p>

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
