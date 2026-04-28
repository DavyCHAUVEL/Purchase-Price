# RUNBOOK - T-000238 Ajout INCI dans les mails auto des meeting reports

## Ticket
- Name: T-000238 Ajout INCI dans les mails auto des meeting reports
- Id: a0NIV00001qVEQ12AO
- Subject: Ajout INCI dans les mails auto des meeting reports
- Status: New
- LastModifiedDate: 2026-04-24T15:47:46.000+0000

## Contexte
<p>Dans les mails Auto des meetings reports, peut-on ajouter l&#39;INCI des ingr├⌐dients, en italique, apr├¿s le nom commercial de l&#39;ingr├⌐dient qui a ├⌐t├⌐ pr├⌐sent├⌐ ?</p><p>Si l&#39;ingr├⌐dients contient plusieurs INCI, pas besoin de la d├⌐composition en %, juste la liste des INCI avec des (&amp;) entre chaque ingr├⌐dient. </p><p>Les clients nous ont fait la remarque que les INCI n&#39;apparaissent pas dans les mails de retours de visites que nous envoyons. Cela leur serait utile pour faire des recherches ├á partir des mails reports qui leurs sont envoy├⌐s aussi bien par nous que par les autres fournisseurs. Car actuellement s&#39;ils font une recherche dans leur boite mail par INCI, nos reports de ressortent pas. </p><p><br></p><p>Cela leur permet aussi de voir rapidement si l&#39;ingr├⌐dient est dans de l&#39;eau, s&#39;il a des conservateurs....</p><p><br></p>

## Low-Code Analysis (obligatoire)
- Validation Rule: Non pertinent (demande de rendu email, pas de contrainte de saisie).
- Formula Field: Limite atteinte, car la liste INCI doit etre construite dynamiquement par produit presente a partir des composants lies.
- Record-Triggered Flow: Solution retenue via extension declarative du flow `Email_Meeting_Report` (autolaunched) sans Apex.
- Scheduled Flow: Non pertinent (generation immediate du body email sur action meeting report).

## Flow Limitation (si Apex requis)
- Raison technique precise: N/A (Apex non requis).

## Implementation Traceability

### Done
- `Email_Meeting_Report.flow-meta.xml` mis a jour pour:
  - Recuperer les `Component__c` du produit courant (`Get_Product_Components`).
  - Boucler sur les composants (`Loop_Through_Product_Components`) et construire la liste INCI avec separateur `&amp;`.
  - Afficher l'INCI en italique apres le nom commercial via `DisplayProductNameWithINCI`.
- Deploiement effectue en sandbox `ma-sandbox`:
  - `sf project deploy start --target-org ma-sandbox --source-dir "force-app/main/default/flows/Email_Meeting_Report.flow-meta.xml"`
  - Deploy ID: `0AfAW00000lj7K30AI`
  - Status: `Succeeded`

### Remaining
- Validation fonctionnelle metier en UI Salesforce sur un meeting report contenant:
  - 1 ingredient (affichage INCI unique),
  - plusieurs ingredients (separateur `&`),
  - ingredient sans INCI (pas d'ajout entre parentheses).
- Activation manuelle de la nouvelle version du flow si necessaire selon governance org (le metadata du flow est en statut `Draft`).

### Risks/Assumptions
- Hypothese: la source INCI attendue est `Component__c.Substance_INCI__c` (derivee de `Substance__r.INCI_Name__c`).
- Si un produit contient des composants dupliques, la liste peut afficher des INCI repetes (aucune deduplication ajoutee dans ce ticket).

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
