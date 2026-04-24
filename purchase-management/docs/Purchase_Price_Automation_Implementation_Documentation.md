# Purchase Price Automation - Documentation complete

## 0) Mise a jour d&apos;offre (métier)

Pour **renouveler / remplacer** une offre de prix, le modèle d&apos;usage n&apos;est **pas** l&apos;édition inline : l&apos;utilisateur **recrée** une copie (bouton / fenêtre) avec champs ajustables ; au **Save**, l&apos;**ancienne** offre est passée en **obsolète** (ou `ToBeReplaced__c` + batch selon vos flux existants) et une **nouvelle** ligne `Purchase_Price__c` est insérée.

**Conséquence pour l&apos;automation** : sur la **nouvelle** ligne, le champ `PP_Revises__c` (lookup auto-référent) **doit** pointer vers l&apos;enregistrement qu&apos;on remplace **à la création** (même empreinte article / fournisseur / purchased threw). Cela alimente le Flow `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` : le contrôle *anti-doublon* ne bloque pas ce cas, mais vérifie la cohérence de l&apos;empreinte avec la cible. Le passage de l&apos;ancienne ligne en *Obsolete* reste géré par votre **Action / Flow / LWC** (ou règles `ToBeReplaced__c` + scheduled déjà en place), dans la même transaction si besoin.

---

## 1) Objectif

Cette documentation decrit l'implementation Salesforce realisee autour de `Purchase_Price__c` dans la sandbox Partial:

- soumission automatique a l'approbation,
- validation metier par approbateurs,
- notifications email apres approbation,
- reporting hebdomadaire,
- parametrage labels/emails.

---

## 2) Perimetre implemente

### 2.1 Approbation (To Confirm -> Validate)

- Un `Purchase_Price__c` cree avec `Status__c = To Confirm` est auto-soumis a l'approval process.
- Approbateurs: Antoine Joubin OU Celine Rotte.
- Regle multi-approbateurs: `FirstResponse`.
- Sortie approval:
  - approve -> `Status__c = Validate`
  - reject -> `Status__c = To Ask`

### 2.2 Notification email temps reel (apres approbation)

- Un email est envoye quand le statut passe de `To Confirm` a `Validate`.
- Le mail contient:
  - un lien cliquable vers le record,
  - des sections business (`IDENTIFICATION`, `PRIX`, `VALIDITE`, `SOURCE`),
  - les champs principaux du prix d'achat.
- Expediteur force sur Org-Wide Email pour eviter les erreurs de domaine non verifie.

### 2.3 Rapport hebdomadaire

- Flow schedule hebdomadaire actif.
- Deux envois:
  - nouveaux prix,
  - records `To Confirm`.

### 2.4 Wizard de creation (Prix + quotations client) — branche a l UI

- **Screen Flow** `PP_PurchasePrice_Screen_CreateWithQuotations` (fichier metadata ci-dessous) : en une session, l utilisateur saisit le `Purchase_Price__c` puis, si **prix specifique client**, un second ecran pour au moins un **Account (Client)** a lier sur `Customer_s_Purchase_Quotation__c`.
- UX ecrans wizard: les champs `Article`, `Supplier` (optionnel) et `Compte client` sont en **selection par nom** (dropdown dynamiques), plus de saisie d ID manuelle.
- Comportement :
  - **Prix standard** : le PP est cree avec `Status = To Confirm` (et `PP_PurchasePrice_AfterSave_AutoSubmitApproval` s enclenche apres l enregistrement).
  - **Prix specifique** : le PP est cree d abord en `To Ask`, puis enregistrement d une `Customer_s_Purchase_Quotation__c`, puis **update** en `To Confirm` (ce changement de statut declenche a son tour l auto-soumission).
- Les autres customers pour le meme pricing pourront completer sur la related list **Customer s Purchase Quotation** sur la fiche Purchase Price.
- Boutons UI:
  - **Fiche Purchase Price**: Quick Action `Create Purchase Price`.
  - **Vue liste Purchase Price**: List Button `Create Purchase Price` (WebLink) a cote de `New`, URL `/flow/PP_PurchasePrice_Screen_CreateWithQuotations`.

---

## 3) Composants metadata modifies/crees

## Flows

- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_AutoSubmitApproval.flow-meta.xml`
  - **Type**: Record-Triggered (After Save, **CreateAndUpdate**)
  - **Role**: auto-submit a l'approval process `Purchase_Price_Validation`.
  - **Filtre Start**: `Status__c = To Confirm` (le record enregistre doit etre a To Confirm)
  - **Decisions + formule** `fxShouldAutoSubmit` : vrai si `ISNEW()` **ou** `ISCHANGED(Status__c)` — pour couvrir un prix cree sans To Confirm puis mis a To Confirm, sans re-soumettre a chaque autre modif quand deja en To Confirm.
  - **Action principale**: `submit`
    - `objectId = $Record.Id`
    - `processDefinitionNameOrId = Purchase_Price_Validation`
    - `submitterId = $User.Id`
    - `skipEntryCriteria = true`

- `force-app/main/default/flows/PP_PurchasePrice_AfterSave_SendApprovalNotification.flow-meta.xml`
  - **Type**: Record-Triggered (After Save, CreateAndUpdate)
  - **Role**: envoi email de notification business.
  - **Condition effective d'envoi**:
    - `NOT(ISNEW())`
    - `ISCHANGED(Status__c)`
    - `Status__c = Validate`
    - `$Record__Prior.Status__c = To Confirm`
  - **Expediteur**:
    - `senderType = OrgWideEmailAddress`
    - `senderAddress = ice.sf-noreply@iwasecosfa.com`
  - **Destinataire courant de test**:
    - user lookup par username `d.chauvel@iwasecosfa.com.partialsb`
  - **Lien record cliquable**:
    - formule `fxRecordUrl` basee sur `$Api.Partner_Server_URL_650 + '/' + $Record.Id`
  - **Prix standard vs spécifique client (email)**:
    - Get Records sur `Customer_s_Purchase_Quotation__c` (`Purchase_Price__c = $Record.Id`), boucle pour concatener `Customer_Account_Name__c` + code auto `Name` dans `varQuotationLines`.
    - Corps HTML : deux lignes — **Type de prix** (`fxEmail_TypePrix` : *Prix standard* ou *Prix spécifique à un ou plusieurs clients*) et **Client(s) concerné(s)** (`fxEmail_ClientsConcernes` : `-` si standard, sinon liste `nom compte [code]`, séparée par `; `).
    - Libellés tableau en français (ex. *Produit d'achat*, *Début / fin de validité*, *Client(s) concerné(s)*).
    - **Textes d’intro** : Custom Labels `LB_Purchase_Price_Email_Body_*` (orthographe FR, entités HTML) — email d’**approbation** : *Un prix d’achat a été approuvé (statut : Validé)*, plus le tableau. Sujet approbation : `… - Approuvé - {Name}`.
    - **Note**: le nom compte vient de `Customer_Account_Name__c` (pas de `Customer__r.Name` dans le Get Records).
  - **Corps email (HTML)**: tableau 2 colonnes; **Article** = `fxArticlePlain` (sans `HYPERLINK`). Fournisseur = `Supplier__r.Name`. Formules scindées `fxBodyTable1` + `fxBodyTable2` + `fxBodyDetail`.

- `force-app/main/default/flows/PP_PurchasePrice_Scheduled_WeeklyReports.flow-meta.xml`
  - **Type**: Scheduled Flow (Weekly)
  - **Role**: envoi des rapports hebdomadaires.
  - **Destinataires configures**:
    - nouveaux prix: Antoine Joubin, Antoine Bouhours, Celine Rotte
    - to confirm: Antoine Joubin, Celine Rotte

- `force-app/main/default/flows/PP_PurchasePrice_Scheduled_ManageLifecycle.flow-meta.xml` (etendu)
  - **Type**: Scheduled (quotidien)
  - **Role existant**: `ToBeReplaced` + fin passee -> `Obsolete` ; `Future Price` + debut atteint -> `Validate`.
  - **Nouveau**: si `Status = Validate`, `End_Validity_Date__c` vide, et le champ formule `PP_No_End_Review_Due__c` &lt;= aujourd hui, passage en `To Ask` (open-end, revue 18 mois apres `Start_Validity_Date__c` ou, si vide, 18 mois apres `CreatedDate`).

- `force-app/main/default/objects/Purchase_Price__c/fields/PP_No_End_Review_Due__c.field-meta.xml`
  - **Type**: formule (Date) — `ADDMONTHS(BLANKVALUE(Start, DATEVALUE(CreatedDate)), 18)` seulement si pas de `End_Validity_Date__c`.

- `force-app/main/default/flows/PP_PurchasePrice_Screen_CreateWithQuotations.flow-meta.xml`
  - **Type**: Screen Flow (`processType: Flow`) — **Active** en org partial apres deploiement.
  - **Role**: parcours guide creation `Purchase_Price__c` + option `Customer_s_Purchase_Quotation__c` (jonction) pour les prix specifiques ; voir **2.4**.
  - **UX**: choix dynamiques par nom (`dyn_Article`, `dyn_Supplier_Account`, `dyn_Customer_Account`) pour eviter toute saisie d IDs Salesforce.

- `force-app/main/default/flows/PP_PurchasePrice_BeforeSave_BlockDuplicateStandard.flow-meta.xml`
  - **Type**: Record-Triggered (**RecordBeforeSave**, **Create**)
  - **Role**: empeche un second **prix standard** (cree deja en `To Confirm` — filtre d entree) lorsqu un autre PP non `Obsolete` partage `PP_Dedupe_Fingerprint__c` et que l autre n est pas en `To Ask` et n a pas de quotation client. **Statut metadata** : **Draft** jusqu aux tests (puis activer en prod / partial).

- Champ formule (dedupe)
  - `force-app/main/default/objects/Purchase_Price__c/fields/PP_Dedupe_Fingerprint__c.field-meta.xml` : concat `Article` + `Supplier` + `Article__r.Product__r.Account__c` (Purchased threw).

- Champ lookup (révision d&apos;offre)
  - `force-app/main/default/objects/Purchase_Price__c/fields/PP_Revises__c.field-meta.xml` : à remplir sur le **nouveau** PP lors du parcours &quot;remplacer l&apos;offre&quot; — pointe vers l&apos;ancien PP remplacé (voir **§0**).

- Permission Set (sécurité d&apos;accès)
  - `force-app/main/default/permissionsets/Purchase_Price_Dedupe_Access.permissionset-meta.xml` : lecture sur `PP_Dedupe_Fingerprint__c` + édition sur `PP_Revises__c` sans dépendre du `Admin.profile`.

## Quick Action

- `force-app/main/default/quickActions/Purchase_Price__c.Create_Purchase_Price_With_Quotations.quickAction-meta.xml`
  - **Type**: Flow action
  - **Label**: `Create Purchase Price`
  - **Flow cible**: `PP_PurchasePrice_Screen_CreateWithQuotations`
  - **Usage**: bouton en highlights panel sur la fiche `Purchase_Price__c`.

- `force-app/main/default/quickActions/Purchase_Price__c.Update_Purchase_Price.quickAction-meta.xml`
  - **Label (UI)**: *Update Purchase Price* — le **dev name** de l&apos;action est `Update_Purchase_Price`, mais le **Flow exécuté** est `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` (pas un fichier `Update_Purchase_Price.flow-meta.xml`).
  - **Rôle** : saisie des champs de la &quot;copie&quot; d&apos;offre ; create du nouveau `Purchase_Price__c` puis obsolescence de l&apos;enregistrement d&apos;origine. Les nœuds **Create** du flow renseignent `PP_Revises__c` = ancien `Id` (lien d&apos;audit + compatible règle Before Save de dédup si le statut de création évolue).
  - **Évolutions (fault / données)** : écran d&apos;erreur `Screen_DML_Fault` avec `{!$Flow.FaultMessage}` sur les **Create** et **Update** (plus de fault non géré silencieux) ; **Quotation Link** en champ texte optionnel (plus de composant URL runtime obligatoire) ; **Purchase Price** prérempli depuis l&apos;offre source ; **Supplier__c** recopié sur les deux **Create** ; formule d&apos;expiration future protégée si `Start_Validity_Date` est vide (`IF(ISBLANK(...), $Flow.CurrentDate, ... - 1)`).

## List Button (WebLink)

- `force-app/main/default/objects/Purchase_Price__c/webLinks/Create_Purchase_Price_Wizard.webLink-meta.xml`
  - **Type**: `displayType=button`, `openType=newWindow`
  - **Label**: `Create Purchase Price`
  - **URL**: `/flow/PP_PurchasePrice_Screen_CreateWithQuotations`
  - **Usage**: bouton visible dans la barre de la vue liste Purchase Price (a cote de `New`).

## Approval Process

- `force-app/main/default/approvalProcesses/Purchase_Price__c.Purchase_Price_Validation.approvalProcess-meta.xml`
  - **Entry criteria**: `Status__c = To Confirm`
  - **Approvers**: Antoine Joubin / Celine Rotte
  - **whenMultipleApprovers**: `FirstResponse`
  - **Final approval action**: `Set_Status_Validate`
  - **Final rejection action**: `Set_Status_To_Ask`

## Champ formule (quotation)

- `force-app/main/default/objects/Customer_s_Purchase_Quotation__c/fields/Customer_Account_Name__c.field-meta.xml`
  - **Formule**: `Customer__r.Name`
  - **Role**: permettre au Flow `PP_PurchasePrice_AfterSave_SendApprovalNotification` d'afficher le nom client sans traversee `Customer__r.Name` dans Get Records (refuse par la plateforme sur ce cas).

## Workflow actions utilitaires (utilisees par approval process)

- `force-app/main/default/workflows/Purchase_Price__c.workflow-meta.xml`
  - `Set_Status_Validate` -> `Status__c = Validate`
  - `Set_Status_To_Ask` -> `Status__c = To Ask`

## Labels

- `force-app/main/default/labels/CustomLabels.labels-meta.xml`
  - `LB_Purchase_Price_Email_Subject`
  - `LB_Purchase_Price_Email_Body_New` (création, *vérifier* orthographié correctement)
  - `LB_Purchase_Price_Email_Body_Update` (message **approuvé / Validé**, distinct du scénario création)
  - `LB_Purchase_Price_Weekly_New_Subject`
  - `LB_Purchase_Price_Weekly_ToConfirm_Subject`
  - `LB_Purchase_Price_Weekly_New_Body`
  - `LB_Purchase_Price_Weekly_ToConfirm_Body`

---

## 4) Flux fonctionnel de bout en bout

1. Un utilisateur cree un `Purchase_Price__c` avec `Status__c = To Confirm`.
2. `PP_PurchasePrice_AfterSave_AutoSubmitApproval` soumet automatiquement le record a l'approbation.
3. Salesforce envoie la demande d'approbation a Antoine Joubin / Celine Rotte.
4. Un approbateur valide.
5. L'approval process execute `Set_Status_Validate`.
6. `PP_PurchasePrice_AfterSave_SendApprovalNotification` detecte la transition `To Confirm -> Validate`.
7. Le flow envoie l'email business detaille avec lien record cliquable.

---

## 5) Decisions techniques importantes

- **Org-Wide sender force** (`ice.sf-noreply@iwasecosfa.com`):
  - evite l'erreur `email address domain isn't verified` liee au domaine du user courant.
- **Trigger email uniquement apres approbation**:
  - evite les envois au create brut.
- **Email en body riche (`sendRichBody = true`)**:
  - permet lien HTML cliquable.
- **Description ajoutee dans chaque flow**:
  - facilite maintenance, audit et handover.

---

## 6) Ce qui n'est PAS encore implemente

- **Fait (phase 2 partielle)** : blocage **create** d&apos;un prix **standard** (statut initial `To Confirm` via wizard) si un autre `Purchase_Price__c` **non obsolète** existe déjà avec la même empreinte **Article + Supplier + Purchased threw (compte Product)** et **sans** ligne `Customer_s_Purchase_Quotation__c` sur l&apos;existant. Les enregistrements en `To Ask` (parcours spécifique en cours) ne servent pas de doublon bloquant. Metadata : champ `PP_Dedupe_Fingerprint__c`, Flow **Draft** `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` (Before Save, Create), label `LB_Purchase_Price_Dedupe_Standard_Error`.
- **Reste** : détection doublon **prix spécifiques** (même combinaison + **même ensemble de comptes clients** sur les quotations) — souvent un **sous-flow** ou **Apex** si la matrice de match devient trop lourde pour le builder.
- **Reste** : règle métier historique &quot;nouveau vs update&quot; (spec §44) au sens **loop** sur champs identiques + related list — à cadrer avec les ops (import, révision de prix).

---

## 7) Plan de test recommande

## Cas 1 - Creation To Confirm

1. Creer un `Purchase_Price__c` avec `Status__c = To Confirm`.
2. Verifier qu'une demande d'approbation est creee (Approval History).
3. Verifier reception notif approbation cote Antoine/Celine.

## Cas 2 - Approve

1. Approuver le record.
2. Verifier `Status__c = Validate`.
3. Verifier reception de l'email business:
   - sujet `[Purchase Price] Notification - Approuvé - ...`
   - texte metier attendu
   - lien `Ouvrir le record` cliquable
   - sections business visibles

## Cas 3 - Reject

1. Rejeter le record.
2. Verifier `Status__c = To Ask`.
3. Vérifier l’absence d’e-mail d’approbation (message *Approuvé*).

## Cas 4 - Weekly

1. Attendre le schedule hebdo ou le lancer via debug.
2. Verifier les 2 emails hebdo vers les bons destinataires.

## Cas 5 - Doublon standard (apres activation du Flow Before Save)

1. Créer un PP standard (To Confirm) valide (wizard ou New) pour Article A + Fournisseur F + même compte Purchased threw (produit) P.
2. Tenter un second create **standard** identique : l&apos;enregistrement doit être **rejeté** avec le message d&apos;erreur doublon.
3. Créer un PP **spécifique** (To Ask → quotation → To Confirm) : ne doit **pas** être bloqué seulement parce qu&apos;un standard existe (empreinte identique) — le candidat en To Ask n&apos;est pas traité comme doublon standard.
4. Valider un PP standard existant avec zéro quotation : tenter un nouveau standard identique — doit rester **bloqué**.

---

## 8) Points d'attention maintenance

- Le lookup destinataire dans `PP_PurchasePrice_AfterSave_SendApprovalNotification` pointe actuellement sur Davy (mode test).
- Si retour en prod process:
  - remettre Robin (ou mailing list),
  - conserver `OrgWideEmailAddress` pour fiabiliser l'envoi.
- Si le nom API du process change, mettre a jour `processDefinitionNameOrId` dans le flow d'auto-submit.

---

## 9) Done / Remaining (suivi continu)

## Done

- Email notification enrichi avec hyperlink record Purchase Price.
- Champ Article rendu en hyperlink cliquable avec nom lisible.
- Ajout des champs spec email:
  - Produit d’achat (ex-*Purchase Threw*, via `Article -> Product -> Name`),
  - E-mail : **Type de prix** + **Client(s) concerné(s)**, textes FR et sujet *Approuvé* / *Nouveau* selon le chemin.
  - harmonisation `Supplier`, `Max MOQ`, `Incoterm` sur body d'approbation.
- Champ formule `Customer_Account_Name__c` sur quotation pour alimenter le mail sans `Customer__r.Name` dans le Get Records.
- Wizard mis a jour pour supprimer la saisie manuelle des IDs (selection par nom sur Article / Supplier / Compte client).
- Bouton wizard ajoute en vue liste Purchase Price (List Button) + presence sur fiche (Quick Action).
- Phase 2 doublon **standard** : `PP_Dedupe_Fingerprint__c` + Flow `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` (activer après test sur sandbox).
- Destinataire email approval rendu configurable via label `LB_Purchase_Price_Notification_Recipients` (fallback `robin@iwasecosfa.com`).
- Flow update clone/archive enrichi avec comparatif ancien/nouveau et `% variation` dans l ecran de confirmation.
- Dedupe etendu au cas prix specifique via controle de signature client principale (lookup quotation candidat vs quotation record courant).
- Branche explicite "negociation perdue" ajoutee dans `PP_PurchasePrice_Scheduled_ManageLifecycle` via marqueur `NEGOTIATION_LOST` dans `Quotation_Comment__c` => bascule `Obsolete`.
- Renommage API des 7 flows Purchase Price vers format deployable (sans `__c` dans le nom API): prefixe `PP_PurchasePrice_...`.
- Deploiement sandbox `ma-sandbox` execute avec succes (`Deploy ID: 0AfAW00000lfxKP0AY`) sur flows/quick actions/weblink/labels cibles.

## Remaining

- Dedupe **prix specifique** complet (set integral de quotations clients, pas seulement client principal) a finaliser si exigence metier stricte.
- Loop &quot;nouveau vs update&quot; métier (spec longue) — hors périmètre du flow standard ci-dessus.
- Recette fonctionnelle manuelle complete en sandbox (Cas A-E) a executer cote metier/UI.

## Risks / Assumptions

- L'email reflete la **presence de quotations liees** au Purchase Price, pas uniquement la case `Customer Specific Price` si elle pouvait diverger des donnees (a verifier cote declaratif / synchro checkbox).
- `sf project deploy validate` echoue dans cet org sur couverture globale Apex (70% &lt; 75%) et impacts tests existants ; deploiement effectif realise via `sf project deploy start --test-level NoTestRun` (sandbox uniquement).
- La branche "negociation perdue" repose sur une convention de saisie (`NEGOTIATION_LOST`) dans `Quotation_Comment__c` tant qu aucun champ dedie n est formalise.

---

## 10) Conformite au cahier des charges (Purchase_Price_Automation_Specs)

### 10.1 Conforme

- **Processus existants non modifies**: `Future Price -> Validate` et `Validate -> Obsolete` conserves.
- **Flux approbation**: create `To Confirm` -> soumission auto -> approbateur Antoine/Celine -> issue approve `Validate`.
- **Notification temps reel a la validation**: flow after-save sur transition `To Confirm -> Validate` avec lien record et details metier.
- **Mise a jour via nouveau record**: quick action `Update Purchase Price` pilotee par `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` (nouveau record + ancien obsoletise) et alimentation `PP_Revises__c`.
- **Rapports hebdomadaires principaux**: nouveautes + `To Confirm` avec destinataires metier.
- **Open end / pas de fin de validite (18 mois)**: job quotidien `PP_PurchasePrice_Scheduled_ManageLifecycle` + formule `PP_No_End_Review_Due__c` : `Validate` sans `End_Validity_Date__c` repasse en `To Ask` lorsque aujourd hui >= 18 mois apres le debut (ou apres `Created` si pas de start).
- **Directives techniques**: low-code flow-first, labels email, pas d'IDs hardcodes dans la logique metier.

### 10.2 Partiellement conforme / decision metier

- **Reject approbation**: implementation actuelle `reject -> To Ask`. Le texte spec mentionne une branche de negociation contradictoire (`negociation perdue => Validate`, `gagnee => To Ask`) marquee "a revoir plus tard". Decision actuelle: conserver `To Ask` au rejet.
- **Doublon update/spec loop**: blocage dedupe **standard** implemente; controle integral "meme combinaison + meme ensemble quotations clients" pour prix specifiques reste partiel.
- **Destinataire Robin**: en sandbox, destinataire flow email pointe encore sur utilisateur de test; bascule Robin/listing requise pour alignement final production.

### 10.3 Non couvert a ce stade

- Rapport automatique des `Purchase_Price__c` `Validate` de plus de 18 mois.
- Regle automatique "passer en `To Ask` 2 semaines avant fin de validite" (non livree telle que formulee dans la spec) — differencier de l open-end: **18 mois sans date de fin** -> `To Ask` (voir `PP_PurchasePrice_Scheduled_ManageLifecycle` + `PP_No_End_Review_Due__c`).
- Integration/exigences hors Salesforce mentionnees dans la spec (SharePoint/Excel) non traitees dans ce package metadata.



