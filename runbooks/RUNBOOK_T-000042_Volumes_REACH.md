# RUNBOOK - T-000042 Volumes REACH

## Ticket
- Name: T-000042 Volumes REACH
- Id: a0NIV00001Xe1bO2AR
- Subject: Volumes REACH
- Status: On Hold
- LastModifiedDate: 2025-11-21T16:25:16.000+0000

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
<p><a href="https://newaccount1636726677731.freshdesk.com/a/contacts/101095970879" target="_blank" style="color: rgb(44, 92, 197);">Camille BASSOMPIERRE</a></p><p>┬áreported via the portal</p><p><em>(Fri, 5 Sep 2025 at 2:45 PM)</em></p><p><br></p><p>┬á</p><p><br></p><p>Hello Davy</p><p><br></p><p>Concernant l&#39;objectif de gestion des volumes REACH de EBP dans SF, on a besoin dans SF stpl de ces champs</p><p>au niveau volume: quantit├⌐ vendue, ann├⌐e, mois</p><p>au niveau produit: quantit├⌐ du contrat, ann├⌐e</p><p><br></p><p>un produit peut ainsi avoir une ou plusieurs entr├⌐es au niveau volume</p><p><br></p><p>Il nous faudra ensuite pour les admins et la reg une vue pouvant r├⌐sumer par produit l&#39;historique des volumes, pour comparer ├á la donn├⌐e &quot;contrat&quot; si necessaire</p><p><br></p><p>quand es tu dispo qu&#39;on voit ├ºa stpl</p>

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
