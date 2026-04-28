# RUNBOOK - T-000130 suivi envoi echant

## Ticket
- Name: T-000130 suivi envoi echant
- Id: a0NIV00001lAheP2AS
- Subject: suivi envoi echant
- Status: In Progress
- LastModifiedDate: 2026-04-17T16:14:57.000+0000

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
<p>hello</p><p><br></p><p>lors de l&#39;envoi automatique des emails de suivi des envois d&#39;├⌐chant ├á 30 &amp; 60j , le tableau du bas de l&#39;email est l&#39;ancienne version (pas de lien avec COA) :</p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001lAheP&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000Wm008" alt="image.png"></img></p><p><br></p><p><br></p><p>serait il possible que ce soit celui actuel qui apparaisse car parfois les clients nous demande le COA s&#39;ils n&#39;arrivent pas ├á ouvrir ou n&#39;ont plus lemail et donc ce serait plus simple pour nous d&#39;avoir ce tableau l├á : </p><p><br></p><p><img src="https://iwasecosfaeurope.file.force.com/servlet/rtaImage?eid=a0NIV00001lAheP&amp;feoid=00NIV00001TWL24&amp;refid=0EMIV00000WmFS9" alt="image.png"></img></p><p><br></p><p>merci</p><p><br></p><p><br></p><p><br></p><p><br></p>

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
