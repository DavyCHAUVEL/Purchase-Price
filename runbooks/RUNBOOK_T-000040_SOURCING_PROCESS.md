# RUNBOOK - T-000040 SOURCING PROCESS

## Ticket
- Name: T-000040 SOURCING PROCESS
- Id: a0NIV00001XCW752AH
- Subject: SOURCING PROCESS
- Status: In Progress
- LastModifiedDate: 2025-11-07T16:07:30.000+0000

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
<p>Hello,┬á</p><p><br></p><p>concernant le process de sourcing il faut ajouter les task suivantes :</p><p>- demande ├⌐chantillon au fournisseur pour Sylvie</p><p>-Demande d&#39;ajout dans le calculateur pour purchase team</p><p>- Lancement du plan de marketing et communication sales</p><p><br></p><p><br></p><p>Cette ├⌐tape arrivera apr├⌐s la task ranking et la task r├⌐glementaire de Sara !┬á</p><p><br></p><p>Pour en re discuter !</p>

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
