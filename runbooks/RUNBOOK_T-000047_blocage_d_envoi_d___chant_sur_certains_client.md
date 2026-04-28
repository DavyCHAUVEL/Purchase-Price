# RUNBOOK - T-000047 blocage d'envoi d'├⌐chant sur certains client

## Ticket
- Name: T-000047 blocage d'envoi d'├⌐chant sur certains client
- Id: a0NIV00001YXAcP2AX
- Subject: blocage d'envoi d'├⌐chant sur certains client
- Status: In Progress
- LastModifiedDate: 2025-10-17T15:29:14.000+0000

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
<p>hello</p><p><br></p><p>Par exemple, dans le fichier client il y a Berkem ├á qui on ne doit rien envoyer concernant les esters de polyglycerol.</p><p>serait il possible , d&#39;avoir comme pour les ├⌐chant necessitant un NDA, une case a cocher si on veut lui faire partir un autre ├⌐chant; ce serait une alerte.</p><p>si oui, je vais demander aux commerciaux la liste des clients dans ce cas. merci</p>

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
