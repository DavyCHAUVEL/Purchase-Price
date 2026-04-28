# RUNBOOK - T-000237 Automatisation cr├⌐ation ├⌐tiquette nouvel article

## Ticket
- Name: T-000237 Automatisation cr├⌐ation ├⌐tiquette nouvel article
- Id: a0NIV00001qRkWX2A0
- Subject: Automatisation cr├⌐ation ├⌐tiquette nouvel article
- Status: New
- LastModifiedDate: 2026-04-23T14:36:14.000+0000

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
<p>Quand une fiche nouvel article est cr├⌐├⌐├⌐, je crois  qu&#39;il y a une proc├⌐dure pour que le document FTA correspondant soit ├⌐galement cr├⌐├⌐ dans la foul├⌐e. Est-ce possible d&#39;ajouter dans la porc├⌐dure la cr├⌐ation syst├⌐matique de l&#39;├⌐tiquette Excel  &quot;ICE LABEL&quot; pour ce nouvel article + classement dans le dossier partag├⌐ d├⌐di├⌐ ? Dans l&#39;id├⌐al, une notification ├á l&#39;ADV (disponibilit├⌐ des documents) est-elle possible (car envoi n├⌐c├⌐ssaire au Japon avant import) ? Merci !</p>

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
