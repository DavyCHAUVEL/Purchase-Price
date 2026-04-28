# RUNBOOK - T-000139 Probl├¿me sur lien sharepoint lors de la maj d'un prix

## Ticket
- Name: T-000139 Probl├¿me sur lien sharepoint lors de la maj d'un prix
- Id: a0NIV00001lXEgX2AW
- Subject: Probl├¿me sur lien sharepoint lors de la maj d'un prix
- Status: Ready_For_Production
- LastModifiedDate: 2026-04-17T15:18:07.000+0000

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
<p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001lXEgX&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000X1g8f" alt="image.png"></img></p><p><span class="ql-cursor">∩╗┐</span><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001lXEgX&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000X1Vd5" alt="image.png"></img></p>

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
