# RUNBOOK - T-000071 Amelioration Product

## Ticket
- Name: T-000071 Amelioration Product
- Id: a0NIV00001aOlyD2AS
- Subject: Amelioration Product
- Status: In Progress
- LastModifiedDate: 2025-11-07T15:33:42.000+0000

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
<p>Hello, </p><p><br></p><p>Apres en avoir discut├⌐ avec Antoine, j&#39;ajoute des tickets de demandes d&#39;am├⌐lioration de Salesforce. </p><p><br></p><p>Pour la cat├⌐gorie produit, ajouter les champs URL :</p><ul><li>Crea LinkedIn</li><li>One Page</li><li>Webinar</li><li>Other</li></ul><p><br></p><p>Egalement, une section &quot;tag&quot; afin de faire des recherches par mot cl├⌐. Exemple &quot;up-cycling&quot; ou &quot;PEG-free&quot;.</p><p><br></p><p>Puis pour chaque ingredient ET fournisseur, une case de zonning (dans quel pays ce produit et ce fournisseur peut il ├¬tre vendu).</p><p><br></p>

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
