# RUNBOOK - T-000232 Ajout d'une case "Comments" Offre de prix

## Ticket
- Name: T-000232 Ajout d'une case "Comments" Offre de prix
- Id: a0NIV00001qG7QV2A0
- Subject: Ajout d'une case "Comments" Offre de prix
- Status: New
- LastModifiedDate: 2026-04-21T06:40:16.000+0000

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
<p>Y aurait-il la possibilit├⌐ d&#39;ajouter un encart pour pouvoir ajouter des commentaires dans nos offres de prix ?</p><p><br></p><p>Dans l&#39;exemple ci-dessous, nous avons transmis des prix pour des MOQ par livraisons, mais ces prix par livraisons sont conditionn├⌐s ├á des volumes annuels. </p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001qG7QV&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000bTk0Z" alt="image.png"></img></p>

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
