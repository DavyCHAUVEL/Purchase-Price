## 2026-07-01 - Arrondi CEILING JPY/KRW (filet universel + affichage)

### Contexte

- Signalement metier: des Purchase Price en JPY s affichaient avec des decimales (ex. 1234,5).
- Le champ `Purchase_Price__c` est un Number (scale=2): l UI affichait `1235,00` meme apres arrondi.

### Correctif applique

- Flow Before Save `PP_PurchasePrice_BeforeSave_CeilingJpyKrw`:
  - entree: `CurrencyIsoCode` = JPY ou KRW et `Purchase_Price__c` renseigne,
  - action: `Purchase_Price__c = CEILING(Purchase_Price__c)` a chaque create/update.
- Wizards `PP_PurchasePrice_Screen_CreateWithQuotations` et `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`:
  - CEILING sur la valeur saisie manuellement (JPY/KRW), en plus du recalcul auto.
- Champ formule `Purchase_Price_Formatted__c` + Lightning Page:
  - affichage entier pour JPY/KRW (remplace le Number brut sur la page record).
- `Display_Label__c` et email approbation alignes sur le format entier JPY/KRW.

### Remaining

- Records JPY/KRW deja en base avec decimales: corriger via update wizard ou script one-shot si besoin (pas de masse auto).
- Edition inline du montant brut: toujours possible via formulaire standard; le Before Save arrondit a la sauvegarde.

### Risks/Assumptions

- Le champ technique `Purchase_Price__c` reste Number scale=2; l affichage metier passe par `Purchase_Price_Formatted__c` sur la page PP.

## 2026-04-29 - Update wizard PP: recalcul marge + sequence specific + affichage confirmation

### Contexte

- Pendant les tests via le bouton `Update Purchase Price`, plusieurs comportements ont ete observes:
  - pour un prix specific, erreur validation lors de la creation (`To Confirm` sans quotation encore liee),
  - changement de `Supplier_Margin__c` sans impact visible sur le `Purchase_Price__c`,
  - message de confirmation affichant un "Nouveau prix" identique a l ancien alors que recalcul attendu.

### Correctifs appliques

- Flow `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`:
  - creation du nouveau record en `To Ask` (au lieu de `To Confirm`) pour laisser le temps de cloner les `Customer_s_Purchase_Quotation__c`,
  - passage en `To Confirm` juste avant soumission approval (et apres clonage quotations),
  - ajout d une formule de recalcul du prix (`Formula_CalculatedPurchasePrice`) avec logique devise JPY/KRW (arrondi via `CEILING`),
  - ajout d une formule de decision (`Formula_ShouldRecalculatePurchasePrice`) pour recalculer automatiquement si `Manufacturer_Price__c` ou `Supplier_Margin__c` change,
  - alimentation de `Purchase_Price__c` a la creation via la valeur finale resolue (`Formula_PurchasePrice_For_Create`),
  - correction de l ecran de confirmation: le "Nouveau prix" affiche maintenant la valeur effectivement utilisee en creation (plus la valeur brute saisie ecran).

### Validation

- Deploy sandbox reussis (meme flow, iterations successives de fix):
  - `0AfAW00000ljt260AA`
  - `0AfAW00000ljuHV0AY`
  - `0AfAW00000ljtN40AI`
  - `0AfAW00000ljuiv0AA`

## 2026-04-29 - Approbateurs temporaires (mode test anti-spam)

### Contexte

- Pour eviter le spam pendant la phase de validation, les approbateurs historiques devaient etre temporairement retires.

### Correctif applique

- Approval processes Purchase Price passes en mode test avec approbateur unique:
  - `Purchase_Price__c.Purchase_Price_Validation` -> `d.chauvel@iwasecosfa.com` uniquement,
  - `Purchase_Price__c.Purchase_Price_Validation_Client` -> `d.chauvel@iwasecosfa.com` uniquement.

### Validation

- Deploy sandbox reussi: `0AfAW00000ljsUD0AY`.

## 2026-04-29 - QA scenario update future price (approval/reject)

### Regle fonctionnelle validee

- Si `Update Purchase Price` cree un nouveau PP avec `Start_Validity_Date__c` dans le futur:
  - l ancien PP reste `Validate` (avec `ToBeReplaced__c = true`),
  - le nouveau PP est force en `To Confirm`,
  - la soumission en approbation est lancee.
- Si approbation:
  - transition `To Confirm` -> `Validate`,
  - si date de debut future, le status est force en `Future Price`.
- Si rejet:
  - transition `To Confirm` -> `To Ask`.

### Mini checklist QA (3 cas)

1. **Future + Approve**
   - Preconditions: ancien PP `Validate`, update avec `Start_Validity_Date__c = today + 10`.
   - Attendu avant decision: nouveau PP = `To Confirm`, approval pending; ancien PP toujours `Validate`.
   - Attendu apres approbation: nouveau PP = `Future Price`.

2. **Future + Reject**
   - Preconditions: meme setup future.
   - Attendu avant decision: nouveau PP = `To Confirm`.
   - Attendu apres rejet: nouveau PP = `To Ask`.

3. **Date du jour + Approve (non-future baseline)**
   - Preconditions: update avec `Start_Validity_Date__c = today`.
   - Attendu apres approbation: nouveau PP = `Validate` (pas `Future Price`).

### Flows impactes

- `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`
- `PP_PurchasePrice_BeforeSave_EnforceTypeDefaults`
- `PP_PurchasePrice_AfterSave_SendApprovalNotification`
- `PP_PurchasePrice_AfterSave_SendApproveOwnerNotification`

## 2026-04-29 - Condition d usage prix specifique (Customer Quotation lock)

### Regle metier

- Pour un `Purchase_Price__c` de type **Customer-Specific Price**:
  - si statut = `To Confirm` **ou** `Validate`,
  - l utilisateur ne peut plus ajouter ni reassigner de `Customer_s_Purchase_Quotation__c`.
- Pour ajouter un nouveau client dans ce cas, il faut creer une **nouvelle revision** de Purchase Price.

### Garde-fou technique

- Flow `PP_CustomerPurchaseQuotation_BeforeSave_BlockOnSpecificToConfirm` ajuste:
  - blocage sur creation/changement de lookup quotation si parent specific en `To Confirm` **ou `Validate`**,
  - message utilisateur explicite demandant de creer une nouvelle revision.

## 2026-04-29 - Frise (Path) Lightning : record type Standard Price

### Contexte

- Le Path `Purchase_Price_Status` etait lie au record type `__MASTER__` seul ; les pages Lightning avec RT **Standard Price** n affichaient pas la frise sur `Status__c` (meme logique que pour Customer Specific corrige precedemment).

### Correctif

- Nouveau metadata `PathAssistant` `Purchase_Price_Status_Standard` : `entityName = Purchase_Price__c`, `fieldName = Status__c`, `recordTypeName = Standard_Price`, libelle court `PP Status Standard` (limite metadata).

### Validation

- Deploy sandbox reussi : `PathAssistant:Purchase_Price_Status_Standard` (deploy `0AfAW00000ljnHd0AI`).

## 2026-04-27 - Stabilisation update flow + recap approval specific

### Contexte

- Pendant les tests de mise a jour (ex. `Supplier Margin 3% -> 6%`), le flow `Update Purchase Price` presentait des faults runtime et ne garantissait pas toujours le contexte approval attendu (specific + clients).
- Besoin metier confirme:
  - conserver le record type parent lors d une mise a jour,
  - recopier les quotations clients quand le parent est specific,
  - distinguer explicitement "mise a jour" dans approval/ecran.

### Correctifs appliques

1. **Flow update clone/archive (`PP_PurchasePrice_Screen_UpdateByCloneAndArchive`)**
   - copie explicite `RecordTypeId` depuis le parent sur les 2 chemins create (future/non-future),
   - clone des `Customer_s_Purchase_Quotation__c` lies vers le nouveau PP pour les PP customer-specific,
   - routage approval dynamique:
     - standard -> `Purchase_Price_Validation`
     - customer-specific -> `Purchase_Price_Validation_Client`
   - alimentation `Approval_Context__c` avant soumission,
   - commentaire de soumission explicite "Mise a jour du prix ...",
   - durcissement fault handling: fault connectors ajoutes sur lookups et creates critiques.

2. **Runtime data/fiels compatibility**
   - erreur observee en org: colonnes quotation non resolues selon contexte utilisateur / metadata.
   - correction finale: usage de `Approval_Customer_Name__c` dans le flow update pour le recap clients.

3. **Field + securite**
   - deploiement du champ formule `Customer_s_Purchase_Quotation__c.Approval_Customer_Name__c`,
   - extension du permission set `Purchase_Price_Visibility_Admin_Sales` avec lecture de ce champ (profils metier Admin/Sales).
   - Note: `Sales_User.permissionset-meta.xml` non deployable tel quel dans cet org (erreur historique `Product2.CHINA_NMPA__c`), donc correction FLS portee via permission set dedie.

4. **UX ecran de confirmation update**
   - ajout ancienne/nouvelle `Supplier Margin`,
   - ajout message explicite si prix inchange:
     - "Mise a jour sans changement de Purchase Price (autres champs modifies)."

5. **Normalisation record types (sandbox)**
   - migration data executee:
     - PP avec quotations -> `Customer_Specific_Price` (182 succes, 4 bloques par validation anti-doublon),
     - reste -> `Standard_Price` (827 succes, memes 4 blocages attendus).

### Validation

- Deploiements successifs sandbox reussis sur flow/field/permission set cible.
- Requete SOQL validee apres correctifs:
  - `SELECT Id, Approval_Customer_Name__c, Customer__c FROM Customer_s_Purchase_Quotation__c LIMIT 1`
- Cas update non bloquant confirme: nouveau PP cree avec recap de confirmation.

## 2026-04-27 - Hotfix naming: faux "OBSOLETE -" sur creation prix specifique

### Symptome

- Creation d un `Customer_Specific_Price` avec `Status__c = To Ask`, mais nom affiche avec prefixe `OBSOLETE -`.
- Impression metier que le record passe en obsolete, alors que le statut reste `To Ask`.

### Cause racine

- Le flow `Purchase_Price_Auto_Name` construit le nom depuis `Article__r.Product__r.Name`.
- Sur certains produits deja prefixes `OBSOLETE -`, le nouveau Purchase Price recuperait ce prefixe meme sans statut `Obsolete`.

### Correctif applique

- Flow `Purchase_Price_Auto_Name` mis a jour:
  - suppression du prefixe `OBSOLETE - ` du nom produit source avant construction du `Name` (hors vrais cas obsoletes deja prefixes).
  - activation de la version de flow deployee.
- Correction immediate du record impacte en sandbox:
  - `a0UAW00000A6D4r2AF` renomme en `SY-GLYSTER MO-7S (16 Kg) - MOQ > 1200`.

### Validation

- `FlowDefinition Purchase_Price_Auto_Name`: `ActiveVersion = LatestVersion = 6`.
- Relecture record impacte: `Status__c = To Ask`, nom sans prefixe obsolete.

## 2026-04-27 - Approval page: indicateur explicite prix specifique + clients

### Symptome

- Sur la page de demande d approbation, le contexte "prix specifique" et la liste clients n etaient pas clairement visibles.
- Les commentaires de soumission (`submit.comments`) ne remontaient pas de maniere fiable dans le panneau "Comments".

### Correctif applique

- Nouveau champ `Purchase_Price__c.Approval_Context__c` (Text 255), alimente automatiquement juste avant soumission:
  - Standard: `Standard Price`
  - Specific: `Customer-Specific Price | Clients: <liste clients>`
- Flow `PP_PurchasePrice_AfterSave_AutoSubmitApproval` mis a jour:
  - construction du contexte a partir des `Customer_s_Purchase_Quotation__c`,
  - ecriture du contexte sur le PP,
  - puis soumission vers le process adequat.
- Les 2 Approval Processes affichent maintenant `Approval_Context__c` dans les `approvalPageFields`:
  - `Purchase_Price_Validation`
  - `Purchase_Price_Validation_Client`
- Permission set `Purchase_Price_Visibility_Admin_Sales` etendu avec lecture `Purchase_Price__c.Approval_Context__c`.

### Validation

- Deploy sandbox reussi (flow + approval processes + field + permission set).
- Les demandes deja en cours gardent `Approval_Context__c = null`; seules les nouvelles soumissions afficheront le contexte complet.

### Ajustement complementaire (detail noms clients)

- Ajout du champ formule technique `Customer_s_Purchase_Quotation__c.Approval_Customer_Name__c` (`Customer__r.Name`) pour fiabiliser la recuperation du nom client dans le flow de soumission approval.
- Le flow `PP_PurchasePrice_AfterSave_AutoSubmitApproval` utilise maintenant ce champ pour construire:
  - `Customer-Specific Price | Clients: <Nom Client 1> [CQ-xxxxx]; <Nom Client 2> [CQ-yyyyy]`.

## 2026-04-27 - Rework creation model (Record Types standard vs customer-specific)

### Contexte

- Changement de modele fonctionnel demande:
  - abandon du parcours bouton + screen flow pour la creation cible,
  - passage a 2 record types sur `Purchase_Price__c`,
  - garde-fous renforces sur les statuts et les `Customer_s_Purchase_Quotation__c`.

### Changements appliques

- **Record Types `Purchase_Price__c`**
  - `Standard_Price` (`Standard Price`) avec valeur par defaut `Status__c = To Confirm`.
  - `Customer_Specific_Price` (`Customer-Specific Price`) avec valeur par defaut `Status__c = To Ask`.

- **Nouveau champ technique**
  - `PP_Is_Customer_Specific_Type__c` (formula checkbox) pour identifier le record type `Customer_Specific_Price` sans RecordTypeId hardcode.

- **Flows**
  - Nouveau `PP_PurchasePrice_BeforeSave_EnforceTypeDefaults`:
    - aligne `Customer_Specific__c` sur le record type,
    - force `To Ask` a la creation des prix customer-specific.
  - `PP_PurchasePrice_BeforeSave_GuardClientSpecificToConfirm` adapte:
    - garde-fou base sur `PP_Is_Customer_Specific_Type__c`,
    - `To Confirm` interdit sans au moins 1 quotation liee.
  - Nouveau `PP_CustomerPurchaseQuotation_BeforeSave_BlockOnSpecificToConfirm`:
    - interdit ajout/reassignation de quotation si le PP cible est customer-specific et deja en `To Confirm` (il faut recreer un PP).

- **UI**
  - Retrait du list button wizard dans `Purchase_Price__c.searchLayouts` (creation cible via `New` standard + choix record type).
  - Le screen flow historique reste en metadata mais est marque comme parcours legacy dans la documentation.

- **Emails**
  - Le flow `PP_PurchasePrice_AfterSave_SendApprovalNotification` conserve 2 branches standard/specifique, avec typologie pilotee par le champ technique de record type.

- **Securite**
  - `Sales_User` et `Purchase_Price_Dedupe_Access` etendus pour lecture du champ technique.
  - `Sales_User` etendu avec visibilite des 2 record types `Purchase_Price__c`.

### Fichiers touches

- `force-app/main/default/objects/Purchase_Price__c/recordTypes/Standard_Price.recordType-meta.xml`
- `force-app/main/default/objects/Purchase_Price__c/recordTypes/Customer_Specific_Price.recordType-meta.xml`
- `force-app/main/default/objects/Purchase_Price__c/fields/PP_Is_Customer_Specific_Type__c.field-meta.xml`
- `force-app/main/default/objects/Purchase_Price__c/Purchase_Price__c.object-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_BeforeSave_EnforceTypeDefaults.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_BeforeSave_GuardClientSpecificToConfirm.flow-meta.xml`
- `force-app/main/default/flows/PP_CustomerPurchaseQuotation_BeforeSave_BlockOnSpecificToConfirm.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_SendApprovalNotification.flow-meta.xml`
- `force-app/main/default/permissionsets/Sales_User.permissionset-meta.xml`
- `force-app/main/default/permissionsets/Purchase_Price_Dedupe_Access.permissionset-meta.xml`
- `purchase-management/docs/Purchase_Price_Automation_Implementation_Documentation.md`

### Validation

- Validation locale metadata realisee (structure + coherence references).
- Deploiement sandbox et recette metier a executer sur:
  - creation `Standard Price`,
  - creation `Customer-Specific Price` (status force `To Ask`),
  - blocage `To Confirm` sans quotation,
  - blocage ajout quotation sur PP customer-specific deja en `To Confirm`.

### Extension approvals (meme jour)

- Ajout d un second process d approbation:
  - `Purchase_Price_Validation` => **Standard** only (`Customer_Specific__c = false/null`)
  - `Purchase_Price_Validation_Customer_Specific` => **Customer-Specific** only (`Customer_Specific__c = true`)
- Update du flow `PP_PurchasePrice_AfterSave_AutoSubmitApproval`:
  - routing automatique vers le process standard ou customer-specific selon `$Record.Customer_Specific__c`.
- Objectif: titres/process distincts et base propre pour des notifications et escalades differentes par type.
- Cas multi-clients confirme:
  - un meme PP customer-specific peut porter plusieurs `Customer_s_Purchase_Quotation__c`,
  - le process client couvre ce cas ; le flow email post-approval envoie deja un email par quotation/client.

# Changelog - Purchase Price Automation

Ce fichier sert de journal technique persistant pour tracer les changements effectues sur l'automatisation Purchase Price.

## 2026-04-27 - Patch sandbox: open-end review field + destinataire Robin

### Contexte

- Audit sandbox `ma-sandbox`: ecart detecte entre la doc et l'org sur le champ `PP_No_End_Review_Due__c` (absent en org) et destinataire notification encore positionne sur utilisateur test.

### Correctifs appliques

1. Champ formule open-end revue 18 mois
   - Composant: `Purchase_Price__c.PP_No_End_Review_Due__c`
   - Type: `Formula (Date)`
   - Role: fournir la date de revue (`Start_Validity_Date__c` ou `CreatedDate` + 18 mois) pour les prix `Validate` sans `End_Validity_Date__c`, consommee par le lifecycle quotidien.

2. Destinataire notification approval
   - Label: `LB_Purchase_Price_Notification_Recipients`
   - Valeur patchée: `robin@iwasecosfa.com`

### Validation

- Verification Tooling API:
  - `FieldDefinition` confirme `PP_No_End_Review_Due__c` present (`Formula (Date)`).
  - `ExternalString` confirme `LB_Purchase_Price_Notification_Recipients = robin@iwasecosfa.com`.

### Deploiement

- Commande: `sf project deploy start --target-org ma-sandbox --metadata "CustomField:Purchase_Price__c.PP_No_End_Review_Due__c" --metadata "CustomLabel:LB_Purchase_Price_Notification_Recipients" --test-level NoTestRun --wait 60`
- Deploy ID: `0AfAW00000lhhwH0AQ`
- Resultat: `Succeeded`

### Correction FLS post-patch

- Symptome: champ `PP_No_End_Review_Due__c` present en Tooling API mais non queryable en SOQL session metier (`No such column`).
- Cause: absence de lecture FLS dans le permission set assigne a l'utilisateur de test.
- Correctif: ajout de `Purchase_Price__c.PP_No_End_Review_Due__c` (read-only) dans `Purchase_Price_Dedupe_Access`.
- Deploy ID: `0AfAW00000lhfck0AA` (`Succeeded`).

### Recette technique rapide (sandbox)

- Test auto-submit approval: creation d'un `Purchase_Price__c` temporaire en `To Confirm` -> `ProcessInstance` `Pending` cree (`TargetObjectId = a0UAW00000A60612AB`), confirmant le declenchement du flow `PP_PurchasePrice_AfterSave_AutoSubmitApproval`.
- Nettoyage: record de test supprime apres verification.
- Verification complementaire: instances `Approved` historiques presentes sur `Purchase_Price__c` (branche approve operationnelle). Les cas reject/weekly restent en recette UI metier.

## 2026-04-24 - Job quotidien : expiration des prix Validate (fin de validite passee)

### Comportement

- Le flow planifie **deja** `PP_PurchasePrice_Scheduled_ManageLifecycle` (quotidien, ex. 23:45 UTC). Ajout d une etape **en tete** : tout `Purchase_Price__c` en **Validate** avec `End_Validity_Date__c` renseignee et **&lt;=** `$Flow.CurrentDate` passe en **Obsolete** (les prix sans date de fin restent geres par la regle 18 mois → To Ask).
- Le record-triggered **Purchase Price — Prefix Name On Obsolete** continue de prefixer le `Name` quand le statut devient Obsolete.

### Fichier

- `PP_PurchasePrice_Scheduled_ManageLifecycle` : element `Update_Expired_Validate_To_Obsolete` → puis enchainement existant (ToBeReplaced, Future Price, etc.).

## 2026-04-24 - Notification : 1 email par ligne client (prix specifique)

### Comportement

- **Prix standard** (aucun enregistrement `Customer_s_Purchase_Quotation__c` lie au PP au moment de la validation) : **un seul** email recapitulatif (inchangé).
- **Prix specifique** (au moins une ligne de jonction) : **un email par** ligne `Customer_s_Purchase_Quotation__c`, meme structure HTML (tableau), sujet et ligne client distincts pour chaque envoi.

### Fichier

- `PP_PurchasePrice_AfterSave_SendApprovalNotification` : apres la boucle qui concatene les clients, decision `Specific_Or_Standard` ; branche specifique = boucle `Loop_Email_Each_Quotation` + action `Send_Email_Specific_Per_Quotation` (emailSimple, memes destinataires OWD).

### Deploiement

- A deployer sur sandbox / prod (ex. `sf project deploy start -m "Flow:PP_PurchasePrice_AfterSave_SendApprovalNotification"`). En cas d erreur CLI transitoire `metadata.transfer:Finalizing`, relancer le deploy ou deployer depuis l IDE.

### FLS — `Customer_Account_Name__c` (jonction)

- **Lecture** (champ formule, non editable) : profils `Admin`, `Sales User` + permission set `Sales_User` (`Sales User`).

### `Customer_Specific__c` (Purchase Price) — seul indicateur « prix client »

- `Customer_Specific_Price__c` **supprime** du modele (redondant avec `Customer_Specific__c`) : metadata retiree, flows / layout / flexipage / FLS ajustes.
- **En org** : apres deploy du reste, supprimer le champ restant dans la sandbox (ou `sf project delete source -m "CustomField:Purchase_Price__c.Customer_Specific_Price__c" -o <org>`). En **prod** : aligner + migration si des PP n avaient que l ancien booleen coche (rare) — `Customer_Specific__c` = true.
- Champs + **mise en page** `Purchase_Price__c-Purchase Price Layout` (section Information, apres `ToBeReplaced__c`).
- **FLS** lecture + edition : `Admin`, `Sales User`, permission set `Sales_User`.

### Lightning Record Page `Purchase_Pirces_Lightning_Page`

- **2026-04-24** : retrieve depuis **prod** (alias CLI `iwase-prod`, user `d.chauvel@iwasecosfa.com`) — base XML = **reference prod** (voir ci-dessous).
- **Inventaire prod (reference)** — a ne pas confondre avec le layout classique `Purchase_Price__c-Purchase Price Layout` qui peut lister d autres champs.
  - **Header** : `force:highlightsPanel`, `numVisibleActions` = 3 — actions : `Purchase_Price__c.Update_Purchase_Price`, `Delete`, `Clone` (pas `Create_Purchase_Price_With_Quotations` ni autre custom header).
  - **Colonne 1 (Information)** : `Article__c`, `Manufacturer_Price__c`, `Supplier_Margin__c`, `Purchase_Price__c`, `CurrencyIsoCode`, `Min_MOQ__c`, `Max_MOQ__c`, `Purchase_Price_Incoterm__c`, `Supplier__c`, `Product_from_Article__c`.
  - **Colonne 2** : `Start_Validity_Date__c`, `End_Validity_Date__c`, `Quotation_Link__c`, `Quotation_Link_Full__c`, `Quotation_Comment__c`, `Status__c`, `Checked_By__c`, `Checked_Date__c`, `ToBeReplaced__c` (readonly).
  - **Autres blocs** : `runtime_sales_pathassistant:pathAssistant` ; related list `Customer_s_Quotations__r` (ADVGRID) : colonnes **uniquement** `Customer__c`, `NAME` ; section System Information (Owner, Created, Last Modified) ; `force:recordDetailPanelMobile`.
  - **Absent en prod sur cette page** : `Hypothetical_purchase_price__c` ; `Customer_Specific__c` ; colonne `Customer_Account_Name__c` sur la liste liee.
- **Apres 2026-04-24 (repo / sandbox cible dev)** : **une** case `Customer_Specific__c` sur le flexipage (apres `ToBeReplaced__c`), en plus de la reference prod, pour l automation prix client.
- Commandes : re-sync prod `sf project retrieve start -m "FlexiPage:Purchase_Pirces_Lightning_Page" --target-org iwase-prod` ; deploiement sandbox `sf project deploy start -m "FlexiPage:Purchase_Pirces_Lightning_Page" --target-org ma-sandbox`

### Sync auto : coche parent quand une ligne CPQ existe

- Flow **After Save** `PP_CustomerPurchaseQuotation_AfterSave_SetParentClientFlags` sur `Customer_s_Purchase_Quotation__c` : si **creation** ou **changement** du lookup `Purchase_Price__c` (et parent non vide), `Customer_Specific__c` = true sur le parent si ce n etait pas deja le cas.
- **Pas** d execution quand on edite seulement d autres champs sur la jonction (ex. commentaire) sans toucher au Purchase Price : les cases parent ne sont pas re-forcées a chaque save.
- Sandbox : deploy OK (ex. `0AfAW00000lgLZe0AM`). En cas d erreur CLI `metadata.transfer:Finalizing`, verifier le statut avec `sf project deploy report -i <DeployId> -o ma-sandbox`.

## 2026-04-24 - Hotfixes sandbox (email + approval update)

### Contexte

- Incident 1: email de validation recu en double.
- Incident 2: erreur update via bouton `Update Purchase Price`:
  - `This record is currently in an approval process. A record can be in only one approval process at a time.`

### Correctifs appliques

1. Suppression du doublon email
   - Cause: coexistence de flows after-save legacy + flows renommes actifs en meme temps.
   - Action: desactivation des 2 flows legacy critiques via FlowDefinition (`activeVersionNumber=0`):
     - `PurchasePrice_AfterSave_Sync`
     - `PurchasePrice_Auto_Submit_Approval`
   - Resultat: retour a un seul email de notification constate en test.

2. Correction double soumission approbation sur update
   - Cause: le flow ecran update soumettait deja a l'approbation, puis le flow after-save global tentait une 2e soumission.
   - Action: patch de `PP_PurchasePrice_AfterSave_AutoSubmitApproval`:
     - formule `fxShouldAutoSubmit` restreinte avec `ISBLANK($Record.PP_Revises__c)`.
   - Effet: l'auto-submit ne s'applique plus aux enregistrements crees par le parcours update (clone + archive).

### Validation / Deploiement

- Deploiement desactivations FlowDefinition (sandbox):
  - Deploy ID: `0AfAW00000lg3ZZ0AY`
  - Status: `Succeeded`
- Deploiement patch flow auto-submit (sandbox):
  - Deploy ID: `0AfAW00000lgACn0AM`
  - Status: `Succeeded`

## 2026-04-24 - Nettoyage complet des flows legacy (sandbox)

### Contexte

- Apres le hotfix doublon email (2 after-save), il restait des definitions actives en double sur le meme perimetre (ecran, scheduled, before-save duplique, etc.).

### Action

- Desactivation via FlowDefinition (`activeVersionNumber=0`) des flows legacy suivants (remplaces par le prefixe `PP_PurchasePrice_*`):
  - `Automate_Purchase_Prices_Archive`
  - `Purchase_Price_Block_Duplicate_Standard`
  - `Purchase_Price_Create_with_Quotations`
  - `PurchasePrice_Weekly_Report`
  - `Update_Purchase_Prices_Status`

### Deploiement

- Deploy ID: `0AfAW00000lgHNx0AM`
- Status: `Succeeded`

### Etat cible (actifs PP)

- 7 flows `PP_PurchasePrice_*` + utilitaires `Purchase_Price_Auto_Name` et `Purchase_Price_Prefix_Name_On_Obsolete` (hors perimetre renommage).

## 2026-04-24 - Garde-fou prix client avant To Confirm (Before Save)

### Probleme

- En cochant le(s) champ(s) prix client et en passant en `To Confirm` **avant** d’avoir cree les lignes `Customer_s_Purchase_Quotation__c`, l’approbation / le reste du processus peut se comporter comme un **standard** (aucun enfant encore).

### Solution retenue (la plus fiable, flow-first)

- Nouveau flow **Before Save** `PP_PurchasePrice_BeforeSave_GuardClientSpecificToConfirm` :
  - Entree : `Status = To Confirm` et `Customer_Specific__c` = true.
  - **Creation** : blocage avec message (il faut une 1ere sauvegarde sans To Confirm pour avoir un Id et pouvoir lier les jonctions).
  - **Mise a jour** : `Get Records` sur la 1ere ligne `Customer_s_Purchase_Quotation__c` du PP — si aucune → `Custom Error`.
- Pas de champ roll-up obligatoire (relation enfant = lookup).

### Deploiement

- Deploy ID: `0AfAW00000lgGba0AE` — `Succeeded` (sandbox `ma-sandbox`).

## 2026-04-24 - Gap Closure Plan (Flow-first) - Sandbox

### Contexte

- Execution du plan `purchase-price-gap-closure_25219f4c`.
- Objectif: fermer les ecarts P1/P2 identifies dans le delta CDC vs implementation.
- Contraintes respectees: Flow-first, no Apex, deploy via `sf`.

### Changements implementes

1. Notification destinataire final (P1)
   - Destinataire email rendu configurable via label `LB_Purchase_Price_Notification_Recipients`.
   - Fallback applique: `robin@iwasecosfa.com`.
   - Flow impacte: `PP_PurchasePrice_AfterSave_SendApprovalNotification`.

2. Comparatif ancien/nouveau + pourcentage augmentation (P1)
   - Ajout des formules:
     - `Formula_PreviousPurchasePrice`
     - `Formula_NewPurchasePriceValue`
     - `Formula_PurchasePriceIncreasePercent`
   - Affichage du delta dans l'ecran de confirmation du flow update.
   - Flow impacte: `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`.

3. Dedupe prix specifique (P2)
   - Extension du dedupe standard avec controle de signature client principale (quotation candidate vs quotation record courant).
   - Ajout de l'erreur dediee: `Error_Duplicate_Specific_Price`.
   - Flow impacte: `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard`.

4. Branche "negociation perdue" (P2)
   - Ajout d'une issue explicite dans le lifecycle:
     - `Status = To Ask` + `Quotation_Comment__c` contient `NEGOTIATION_LOST` => `Status = Obsolete`.
   - Flow impacte: `PP_PurchasePrice_Scheduled_ManageLifecycle`.

### Ajustement technique critique

- Renommage API des flows Purchase Price vers format deployable (suppression du pattern avec `__c` dans le nom API flow qui etait interprete comme namespace Salesforce):
  - Prefixe retenu: `PP_PurchasePrice_*`.
- Mise a jour des references associees:
  - Quick Actions
  - WebLink
  - Documentation markdown impactee

### Fichiers principaux touches

- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_AutoSubmitApproval.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_SendApprovalNotification.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_BeforeSave_BlockDuplicateStandard.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_Scheduled_ManageLifecycle.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_Scheduled_WeeklyReports.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_Screen_CreateWithQuotations.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_Screen_UpdateByCloneAndArchive.flow-meta.xml`
- `force-app/main/default/quickActions/Purchase_Price__c.Create_Purchase_Price_With_Quotations.quickAction-meta.xml`
- `force-app/main/default/quickActions/Purchase_Price__c.Update_Purchase_Price.quickAction-meta.xml`
- `force-app/main/default/objects/Purchase_Price__c/webLinks/Create_Purchase_Price_Wizard.webLink-meta.xml`
- `force-app/main/default/labels/CustomLabels.labels-meta.xml`
- `purchase-management/docs/Purchase_Price_Automation_Implementation_Documentation.md`

### Validation / Deploiement

- Validation metadata ciblee:
  - tentative `sf project deploy validate ...`
  - echec non bloquant sur contraintes globales org (coverage Apex globale / tests existants hors perimetre)
- Deploiement effectif sandbox:
  - commande: `sf project deploy start ... --test-level NoTestRun --target-org ma-sandbox --wait 60`
  - resultat: succes
  - Deploy ID: `0AfAW00000lfxKP0AY`

### Commit associe

- Commit: `61734a0`
- Message: `feat: close purchase price flow-first gap plan in sandbox`

### Remaining / vigilance

- Le dedupe specifique est couvre sur signature client principale; extension a un set integral de quotations reste optionnelle selon decision metier.
- La convention `NEGOTIATION_LOST` dans `Quotation_Comment__c` doit etre maintenue tant qu'aucun champ dedie n'est cree.

## 2026-04-29 - Documentation technique des flows (coverage etendu)

### Objectif

- Renforcer la maintenabilite des flows Purchase Price en ajoutant des descriptions explicites sur les elements cles (actions, decisions, formulas, lookups, updates, loops, screens, variables).

### Flows completes dans cette passe

- `force-app/main/default/flows/PP_PurchasePrice_Screen_UpdateByCloneAndArchive.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_SendApprovalNotification.flow-meta.xml`
- `force-app/main/default/flows/PP_PurchasePrice_Scheduled_ManageLifecycle.flow-meta.xml`

### Points documentes

- Raison metier de chaque branche (future vs immediate, standard vs specific, creation vs revision)
- Regles de calcul (recalcul Purchase Price, arrondis JPY/KRW, priorite valeur manuelle)
- Intentions de lifecycle (ToBeReplaced, Future Price, Validate, To Ask, Obsolete)
- Role des variables de contexte approbation et des ecrans utilisateur (succes/erreur)

### Deploiement

- Org: `ma-sandbox`
- Resultat: succes
- Deploy ID: `0AfAW00000ljvFD0AY`

### Harmonisation (safe)

- Uniformisation du style de descriptions en format "Objectif: ..." sur les flows PP deja documentes.
- Corrections mineures de wording (FR coherent, typos, transitions Validate/Future Price explicites).
- Aucun changement de logique, de condition, de routage ni de DML.

## 2026-04-29 - Antidoublon standard assoupli + affichage Record Type

### Objectif

- Eviter les faux blocages antidoublon quand l'utilisateur n'est pas dans un parcours prix standard.
- Afficher le type de record directement sur la fiche Purchase Price pour rendre le contexte explicite.

### Changements

- `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard`:
  - le controle "doublon standard" reste actif pour les enregistrements standards.
  - bypass du chemin antidoublon standard pour les enregistrements customer-specific (`PP_Is_Customer_Specific_Type__c = true`).
- `Purchase_Price__c-Purchase Price Layout`:
  - ajout du champ standard `RecordTypeId` en lecture seule dans la section Information.

### Impact

- Les prix standards gardent la protection anti-doublon.
- Les prix customer-specific ne remontent plus l'erreur doublon standard.
- Le record type est visible directement sur la page record.

## 2026-04-29 - Notifications specific: un seul mail + liste clients verticale

### Objectif

- Eviter le spam de notifications Robin sur les prix customer-specific avec plusieurs clients.
- Rendre la liste des clients plus lisible dans l'email et dans le contexte d'approbation.

### Changements

- `PP_PurchasePrice_AfterSave_SendApprovalNotification`:
  - passage a un envoi unique Robin egalement pour les customer-specific (plus de mail par quotation).
  - formatage de la liste clients en vertical (`<br/>`) avec puces.
- `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`:
  - `Approval_Context__c` liste maintenant les clients sur des lignes separees avec puces.

### Impact

- 1 seul mail Robin par approbation, avec tous les clients affiches les uns sous les autres.
- Contexte d'approbation plus lisible pour les cas customer-specific multi-clients.

## 2026-04-30 - Retouches approval update specific + commentaire approbateur

### Changements

- `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`:
  - correction du recap clients dans `Approval_Context__c` pour les updates future path.
  - fallback sur `Customer__r.Name` si `Approval_Customer_Name__c` est vide.
  - affichage clients en liste verticale dans le contexte d'approbation.
- `PP_PurchasePrice_AfterSave_SendApprovalNotification`:
  - ajout du commentaire approbateur (derniere etape `ProcessInstanceStep` approuvee) dans l'email Robin.
  - placeholder si aucun commentaire n'est saisi.

