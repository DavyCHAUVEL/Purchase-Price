# RUNBOOK - T-000038 demande Expiry date

## Ticket
- Name: T-000038 demande Expiry date
- Id: a0NIV00001We4ht2AB
- Subject: demande Expiry date
- Status: In Progress
- LastModifiedDate: 2025-09-04T13:18:40.000+0000

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
<p>Pour toutes les demandes de Sample Request  ├á Sakamoto, serait il possible qu&#39;une phrase lui apparaisse afin qu&#39;il note sur le COA  la manufacturing date. en effet, les clients veulent connaitre la date d&#39;expiration mais comme il ne veut pas l&#39;indiquer sur le COA des samples, si on a la date de fabrication , on pourra la calculer. le mieux serait qu&#39;il note la date d&#39;expiration mais il ne veut pas...</p>

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
