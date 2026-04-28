# RUNBOOK - T-000234 formulas

## Ticket
- Name: T-000234 formulas
- Id: a0NIV00001qNmv82AC
- Subject: formulas
- Status: New
- LastModifiedDate: 2026-04-22T13:21:56.000+0000

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
<p>dans la formule HYDOR SUNSCREEN, il y a 2 produits a remplacer : FL HAP SC (no push) et  XZ 3000F (obsolete) :</p><p><br></p><p><span style="font-family: Aptos, sans-serif; font-size: 11pt;"><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001qNmv8&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000bb0Ol"></img></span></p><p>mais si je vais sur le produit XZ 3000F il n&#39;est plus obsolete ??????</p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001qNmv8&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000bbSBK" alt="image.png"></img></p><p><br></p><p>il faudrait que les changements de status de produits ne mettent ├á jour dans les formules.</p><p>merci</p>

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
