# RUNBOOK - T-000183 MOQ visibles sur SF

## Ticket
- Name: T-000183 MOQ visibles sur SF
- Id: a0NIV00001ncvi72AA
- Subject: MOQ visibles sur SF
- Status: On Hold
- LastModifiedDate: 2026-04-17T16:13:25.000+0000

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
<p>coucou Ouss et Davy ! </p><p><br></p><p>il y a pas de possibilit├⌐ de voir les diff├⌐rentes MOQ possibles comme dans lΓÇÖexcel. </p><p>serait-il possible de cr├⌐er quelque chose de similaire sur SF ?</p><p><br></p><p>merci d&#39;avance &lt;3</p><p><br></p><p>Francesca</p>

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
