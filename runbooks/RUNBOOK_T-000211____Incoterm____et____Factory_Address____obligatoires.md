# RUNBOOK - T-000211 ΓÇ£IncotermΓÇ¥ et ΓÇ£Factory AddressΓÇ¥ obligatoires

## Ticket
- Name: T-000211 ΓÇ£IncotermΓÇ¥ et ΓÇ£Factory AddressΓÇ¥ obligatoires
- Id: a0NIV00001ohqqt2AA
- Subject: ΓÇ£IncotermΓÇ¥ et ΓÇ£Factory AddressΓÇ¥ obligatoires
- Status: On Hold
- LastModifiedDate: 2026-04-17T16:16:06.000+0000

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
<p>coucou Davy !</p><p><br></p><p>j&#39;esp├¿re que tu vas bien.</p><p><br></p><p>seraitΓÇæil possible dΓÇÖajouter un ast├⌐risque afin de rendre les champs ΓÇ£IncotermΓÇ¥ et ΓÇ£Factory AddressΓÇ¥ obligatoires lors de la cr├⌐ation dΓÇÖun nouvel account ?</p><p><br></p><p>merci beaucoup &lt;3</p><p><br></p><p>Francesca</p>

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
