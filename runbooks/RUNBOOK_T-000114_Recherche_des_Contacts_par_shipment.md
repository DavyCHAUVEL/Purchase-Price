# RUNBOOK - T-000114 Recherche des Contacts par shipment

## Ticket
- Name: T-000114 Recherche des Contacts par shipment
- Id: a0NIV00001ivIRN2A2
- Subject: Recherche des Contacts par shipment
- Status: In Progress
- LastModifiedDate: 2026-01-13T16:21:18.000+0000

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
<p><span style="font-size: 14px;">Revoir le flow┬á</span><span style="font-size: 14px; color: rgb(1, 68, 134);">(AUTOPOPULATE) Shipment Name - Shipment┬á</span><span style="font-size: 14px; color: rgb(0, 0, 0);">car limit├⌐ ├á 5 caract├¿res sur le Pr├⌐nom et le Nom et 20 caract├¿res max sur le nom. =&gt; Soit refaire un champs qui r├⌐cup├¿re le nom complet (sans limitation du nomre de caract├¿res) mais ne r├⌐ponds pas au besoin de la recherche par Nom . Seconde solution laisser juste le NOM de famille dans ll&#39;automatisation du Name. Troisi├¿me solution faire des vues en fonction du besoin.</span></p>

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
