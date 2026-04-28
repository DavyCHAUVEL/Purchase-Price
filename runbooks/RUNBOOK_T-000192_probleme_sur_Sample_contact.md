# RUNBOOK - T-000192 probleme sur Sample contact

## Ticket
- Name: T-000192 probleme sur Sample contact
- Id: a0NIV00001nstcI2AQ
- Subject: probleme sur Sample contact
- Status: In Progress
- LastModifiedDate: 2026-04-17T16:09:31.000+0000

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
<p>HUIWEN ├⌐crit  : </p><p><span style="color: black; font-size: 11.5pt;">Could you please change the name &quot;left the company-angelZHAO&quot; to &quot;kayco&quot; in your system?</span></p><p><br></p><p>quand je vais dans sample contact de Huiwen , Angel ZHAO n&#39;y est plus mais Kayco y est bien.</p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001nstcI&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000Z86CA" alt="image.png"></img></p><p><br></p><p><br></p><p>merci pour votre aide</p><p><br></p>

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
