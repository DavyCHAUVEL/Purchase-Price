# RUNBOOK - T-000207 Sample evaluation feedback email

## Ticket
- Name: T-000207 Sample evaluation feedback email
- Id: a0NIV00001odBj32AE
- Subject: Sample evaluation feedback email
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
<p>Le client re├ºoit un e-mail automatique intitul├⌐ ┬½ Sample evaluation feedback ┬╗ en anglais, alors quΓÇÖil sΓÇÖagit de clients italiens. Cela peut cr├⌐er de la confusion, car nous communiquons en italien : le client me r├⌐pond en anglais puisquΓÇÖil re├ºoit le message en anglais. Serait-il possible de mettre ce mail automatique en italien ? </p><p><br></p><p><br></p><p>Voici un exemple sur lΓÇÖimage ci-dessous.</p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001odBj3&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000ZzL4z" alt="image.png"></img></p><p><br></p><p>Merci :) </p><p><br></p>

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
