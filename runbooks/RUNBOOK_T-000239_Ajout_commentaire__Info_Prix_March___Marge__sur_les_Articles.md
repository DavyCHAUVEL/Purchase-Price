# RUNBOOK - T-000239 Ajout commentaire "Info Prix March├⌐/Marge" sur les Articles

## Ticket
- Name: T-000239 Ajout commentaire "Info Prix March├⌐/Marge" sur les Articles
- Id: a0NIV00001qVDc22AG
- Subject: Ajout commentaire "Info Prix March├⌐/Marge" sur les Articles
- Status: New
- LastModifiedDate: 2026-04-24T16:04:24.000+0000

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
<p>Comme discut├⌐ avec Oussama, j&#39;aimerai pouvoir ajouter un commentaire au niveau des articles pour y indiquer par exemple un pourcentage de marge mini ou max, des infos sur un prix march├⌐ afin que les Sales aient une petite &quot;alerte&quot; lorsqu&#39;ils font un prix de vente. </p><p>Je ne souhaite pas que les Sales puissent modifier ce champs eux-m├¬mes, pour revalider les infos prix march├⌐s de mon c├┤t├⌐, avant de les partager. </p>

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
