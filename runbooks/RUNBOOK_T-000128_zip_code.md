# RUNBOOK - T-000128 zip code

## Ticket
- Name: T-000128 zip code
- Id: a0NIV00001l3EAv2AM
- Subject: zip code
- Status: In Progress
- LastModifiedDate: 2026-01-16T10:09:38.000+0000

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
<p>davy,</p><p>j&#39;avais constat├⌐ que le zip code n&#39;apparaissait plus et tu as rectif├⌐ le tir de suite .</p><p>toutefois, des comptes clients ont ├⌐t├⌐ cr├⌐├⌐s entretemps et quand ils ont ├⌐t├⌐ export├⌐s dans le calculator, ces zip codes sont manquants.</p><p>pourrais tu sortir la liste compl├¿te afin que chaque ITC compl├¿te les manquants et que vous r├⌐int├⌐gri├⌐s ce tableau dans SF. J&#39;en ai discut├⌐ avec Oussama si tu veux plus de d├⌐tail.</p><p>merci</p>

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
