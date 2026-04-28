# RUNBOOK - T-000062 Automatisation Suppliers Reports

## Ticket
- Name: T-000062 Automatisation Suppliers Reports
- Id: a0NIV00001ZQ3Yf2AL
- Subject: Automatisation Suppliers Reports
- Status: On Hold
- LastModifiedDate: 2025-10-10T15:58:52.000+0000

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
<p><a href="https://iwasecosfaeurope-my.sharepoint.com/:b:/g/personal/p_noeuveglise_iwasecosfa_com/EZuaAx5-MpxClJBy0EGkyWAB99IJuOQrBFLlsE6OHNJ5Sw?e=SBfeKi" target="_blank">Tutoriel fait par Robin</a></p><p><br></p><p>Tous les mois Robin fait un export de SF vers un excel pour faire un suplier report</p><p>Peut on l&#39;automatiser d├¿s ce mois</p><p>cf pdf en lien</p><p>si ├ºa ne marche pas le lien, me dire (mais d├⌐j├á envoy├⌐ ├á Davy par Teams)</p>

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
