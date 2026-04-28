# RUNBOOK - T-000233 Probl├¿me cr├⌐ation de presented product

## Ticket
- Name: T-000233 Probl├¿me cr├⌐ation de presented product
- Id: a0NIV00001qIb5t2AC
- Subject: Probl├¿me cr├⌐ation de presented product
- Status: New
- LastModifiedDate: 2026-04-21T07:47:54.000+0000

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
<p>Coucou, je suis en train d&#39;essayer d&#39;ajouter un presented product pour ensuite cr├⌐er une opportunit├⌐, mais il y a un bug je pense je te mets une photo ci-dessous pour que ca soit plus clair. SI je ne coche pas &quot;Create opportunity&quot; il ne se passe rien, et si je coche &quot;create opportunity&quot; ce me mets le message ci-dessous:</p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001qIb5t&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000bViCQ" alt="image.png"></img></p><p>Merci !</p>

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
