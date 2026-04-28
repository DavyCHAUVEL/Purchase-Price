# RUNBOOK - T-000133 Copier les infos des Adresses dans les nouveaux champs

## Ticket
- Name: T-000133 Copier les infos des Adresses dans les nouveaux champs
- Id: a0NIV00001lGbeO2AS
- Subject: Copier les infos des Adresses dans les nouveaux champs
- Status: In Progress
- LastModifiedDate: 2026-01-26T08:06:14.000+0000

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
<p>Hello Davy,</p><p>J&#39;ai besoin de ton aide stp. peux tu :</p><ol><li>Copier les valeurs du champ <span style="font-size: inherit;">Production Postal Code dans Factory Address Postal Code</span></li><li>Copier les valeurs du champ <span style="font-size: inherit;">Shipping Address of the Sample dans Factory Address</span></li><li><span style="font-size: inherit;">Copier le pays de Shipping Address of the Sample dans Factory Address Country</span></li></ol><p>Les champs sont dans l&#39;objet account (Customers).</p><p><br></p><p>Apr├¿s peux tu stp faire l&#39;extraction de ces champs? Pour qu&#39;on puisse les comparer par client et changer quand l&#39;adresse d&#39;envoi des ├⌐chantillons est diff├⌐rente de l&#39;adresse de production. La v├⌐rificaton on le fera dans le point Ticket.</p><p><br></p><p>Je te remecie par avance.</p>

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
