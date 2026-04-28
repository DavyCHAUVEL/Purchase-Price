# RUNBOOK - T-000171 Int├⌐gration JOTFORM dans SF

## Ticket
- Name: T-000171 Int├⌐gration JOTFORM dans SF
- Id: a0NIV00001nHh732AC
- Subject: Int├⌐gration JOTFORM dans SF
- Status: In Progress
- LastModifiedDate: 2026-03-27T16:42:57.000+0000

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
<p>Certaines infos produits (pays de manufacture par ex) ne sont pas pr├⌐sentes directement dans SF, elles sont seulement inscrites dans le lien du Global Certificate. Pour avoir l&#39;info du pays de manufacture, il faut actuellement cliquer sur le lien du GC et chercher l&#39;info. </p><p>Serait-il possible d&#39;int├⌐grer automatiquement le pays de manufacture dans SF en se basant sur la r├⌐ponse indiqu├⌐e dans le GC ? </p>

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
