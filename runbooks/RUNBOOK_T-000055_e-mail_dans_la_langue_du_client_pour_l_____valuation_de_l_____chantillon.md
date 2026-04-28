# RUNBOOK - T-000055 e-mail dans la langue du client pour lΓÇÖ├⌐valuation de lΓÇÖ├⌐chantillon

## Ticket
- Name: T-000055 e-mail dans la langue du client pour lΓÇÖ├⌐valuation de lΓÇÖ├⌐chantillon
- Id: a0NIV00001Ytf1k2AB
- Subject: e-mail dans la langue du client pour lΓÇÖ├⌐valuation de lΓÇÖ├⌐chantillon
- Status: On Hold
- LastModifiedDate: 2025-10-10T16:01:31.000+0000

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
<p>Est-il possible dΓÇÖenvoyer lΓÇÖe-mail automatique de retour sur lΓÇÖ├⌐valuation dΓÇÖ├⌐chantillon dans la langue du destinataire ? Et peut-il ├¬tre envoy├⌐ par le commercial en charge ?</p><p>Merci!! </p>

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
