# Purchase Price - Pack pre-rempli (Sales + Marketing + Data Cloud)

## 1) Inventaire Flow Purchase Price (etat actuel -> cible)

| Etat | Flow actuel | API | Cible recommandee (convention) | Action |
|---|---|---:|---|---|
| OK | `PP_PurchasePrice_AfterSave_AutoSubmitApproval` | 65.0 | `PP_PurchasePrice_AfterSave_AutoSubmitApproval` | Renommer (lisibilite) |
| OK | `PP_PurchasePrice_AfterSave_SendApprovalNotification` | 65.0 | `PP_PurchasePrice_AfterSave_SendApprovalNotification` | Renommer (sens metier) |
| OK | `PP_PurchasePrice_Scheduled_WeeklyReports` | 65.0 | `PP_PurchasePrice_Scheduled_WeeklyReports` | Renommer |
| OK | `PP_PurchasePrice_Scheduled_ManageLifecycle` | 65.0 | `PP_PurchasePrice_Scheduled_ManageLifecycle` | Renommer |
| Draft | `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` | 65.0 | `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` | Activer apres tests |
| OK | `PP_PurchasePrice_Screen_CreateWithQuotations` | 65.0 | `PP_PurchasePrice_Screen_CreateWithQuotations` | Renommer |
| OK | `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` | 65.0 | `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` | Renommer |
| Legacy | `flow_3xOqWlAd9AvyasZk` | 66.0 | `PP_<Object>_<Trigger>_<Intent>` | Identifier + renommer |
| Legacy | `flow_v93EAoHO9OhWbZMH` | 66.0 | `PP_<Object>_<Trigger>_<Intent>` | Identifier + renommer |
| Legacy | `flow_5euzhHwpjNPcDEoy` | 66.0 | `PP_<Object>_<Trigger>_<Intent>` | Identifier + renommer |
| Legacy | `flow_mFwvUINiH2a89Zd2` | 65.0 | `PP_<Object>_<Trigger>_<Intent>` | Identifier + renommer |

## 2) Check API versions (pre-rempli)

Constat repo: les flows globaux ont des versions melangees (`51.0` a `66.0`).

Decision recommandee pour Purchase Management:

- cible immediate: **65.0+** pour tous les flows du domaine Purchase Price
- cible long terme: baseline unique par release train (ex: 66.0)

Commandes utiles:

```powershell
rg "<apiVersion>" "force-app/main/default/flows" -g "*.flow-meta.xml"
```

```powershell
rg "flow_[A-Za-z0-9_]+" "force-app/main/default/flows" -g "*.flow-meta.xml"
```

## 3) Data Stream pre-rempli (Sales -> Data Cloud)

| Source System | Source Object | Source Field API | Data Type | DSO Name (propose) | DSO Field | Sync Frequency | Filter Rule | Why Needed |
|---|---|---|---|---|---|---|---|---|
| Sales Cloud | `Purchase_Price__c` | `Id` | Id | `DSO_PurchasePrice` | `purchasePriceId` | Near-real-time | `Status__c IN ('Validate','To Confirm','To Ask')` | clé primaire |
| Sales Cloud | `Purchase_Price__c` | `Name` | Text | `DSO_PurchasePrice` | `purchasePriceCode` | Near-real-time | idem | identifiant metier |
| Sales Cloud | `Purchase_Price__c` | `Status__c` | Picklist | `DSO_PurchasePrice` | `status` | Near-real-time | idem | segmentation lifecycle |
| Sales Cloud | `Purchase_Price__c` | `Start_Validity_Date__c` | Date | `DSO_PurchasePrice` | `validFrom` | Daily | non nul ou futur | activations temporelles |
| Sales Cloud | `Purchase_Price__c` | `End_Validity_Date__c` | Date | `DSO_PurchasePrice` | `validTo` | Daily | null allowed | expiration / to-ask logic |
| Sales Cloud | `Purchase_Price__c` | `Supplier__c` | Lookup | `DSO_PurchasePrice` | `supplierId` | Near-real-time | non nul | analyse fournisseur |
| Sales Cloud | `Purchase_Price__c` | `Article__c` | Lookup | `DSO_PurchasePrice` | `articleId` | Near-real-time | non nul | analyse produit |
| Sales Cloud | `Customer_s_Purchase_Quotation__c` | `Purchase_Price__c` | Lookup | `DSO_PurchasePriceCustomerQuote` | `purchasePriceId` | Near-real-time | actif seulement | relation client-specifique |
| Sales Cloud | `Customer_s_Purchase_Quotation__c` | `Customer__c` | Lookup | `DSO_PurchasePriceCustomerQuote` | `customerAccountId` | Near-real-time | actif seulement | ciblage compte |
| Sales Cloud | `Customer_s_Purchase_Quotation__c` | `Customer_Account_Name__c` | Formula(Text) | `DSO_PurchasePriceCustomerQuote` | `customerAccountName` | Daily | actif seulement | lisibilite activation |

## 4) DSO -> DMO pre-rempli

| DSO Name | DSO Field | DMO Target (propose) | DMO Field | Transformation Rule | Activation Use Case |
|---|---|---|---|---|---|
| `DSO_PurchasePrice` | `purchasePriceId` | `DMO_PurchasePrice` | `ExternalId` | direct | jointure et dedupe |
| `DSO_PurchasePrice` | `status` | `DMO_PurchasePrice` | `LifecycleStatus` | map picklist 1:1 | exclusion offres obsoletes |
| `DSO_PurchasePrice` | `validFrom` | `DMO_PurchasePrice` | `ValidFromDate` | direct | trigger campagnes pricing |
| `DSO_PurchasePrice` | `validTo` | `DMO_PurchasePrice` | `ValidToDate` | direct | alertes pre-expiration |
| `DSO_PurchasePrice` | `articleId` | `DMO_PurchasePrice` | `ArticleRef` | direct | segmentation produit |
| `DSO_PurchasePrice` | `supplierId` | `DMO_PurchasePrice` | `SupplierRef` | direct | segmentation fournisseur |
| `DSO_PurchasePriceCustomerQuote` | `customerAccountId` | `DMO_PurchasePriceCustomer` | `AccountRef` | direct | activation ABM |
| `DSO_PurchasePriceCustomerQuote` | `purchasePriceId` | `DMO_PurchasePriceCustomer` | `PurchasePriceRef` | direct | targeting prix specifiques |

## 5) Identity Resolution (pre-rempli pour ce domaine)

Ce domaine porte surtout des entites B2B (Account), pas du PII person-level.

Regle:

1. Resolving principal par `Account.Id` (Sales Cloud) pour la couche Purchase Price.
2. Si extension person-level via Marketing Cloud Connect, utiliser `Email` normalise en priorite 1.
3. Eviter un fuzzy matching agressif ici: risque de collisions inutile pour le cas Purchase Price.

## 6) Marketing Cloud Connect - scope filtre (pre-rempli)

| Synced Object | Included Fields (minimum) | Excluded by default | Filter Logic | Rationale |
|---|---|---|---|---|
| `Purchase_Price__c` | `Id`, `Name`, `Status__c`, `Start_Validity_Date__c`, `End_Validity_Date__c`, `Article__c`, `Supplier__c` | champs techniques/debug/non marketing | `Status__c != 'Obsolete'` | reduire volumetrie |
| `Customer_s_Purchase_Quotation__c` | `Id`, `Purchase_Price__c`, `Customer__c`, `Customer_Account_Name__c` | notes internes, champs non activables | active only | ciblage compte-specifique |

## 7) Backlog actionnable (ordre recommande)

1. Renommer les 7 flows Purchase Price en convention cible.
2. Identifier et renommer les flows `flow_*` lies au domaine.
3. Uniformiser API version des flows Purchase Price (`65.0+`).
4. Valider le scope minimal MCC (no full sync).
5. Creer les DSO/DMO ci-dessus et tester un use case d'activation unique.

## 8) Plan exact de renommage + ordre de deploiement CLI (anti-casse)

### 8.1 Mapping exact (old -> new)

| Old API name | New API name |
|---|---|
| `PP_PurchasePrice_AfterSave_AutoSubmitApproval` | `PP_PurchasePrice_AfterSave_AutoSubmitApproval` |
| `PP_PurchasePrice_AfterSave_SendApprovalNotification` | `PP_PurchasePrice_AfterSave_SendApprovalNotification` |
| `PP_PurchasePrice_Scheduled_WeeklyReports` | `PP_PurchasePrice_Scheduled_WeeklyReports` |
| `PP_PurchasePrice_Scheduled_ManageLifecycle` | `PP_PurchasePrice_Scheduled_ManageLifecycle` |
| `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` | `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard` |
| `PP_PurchasePrice_Screen_CreateWithQuotations` | `PP_PurchasePrice_Screen_CreateWithQuotations` |
| `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` | `PP_PurchasePrice_Screen_UpdateByCloneAndArchive` |

### 8.2 References detectees a mettre a jour

1. `force-app/main/default/quickActions/Purchase_Price__c.Create_Purchase_Price_With_Quotations.quickAction-meta.xml`
   - `<flowDefinition>PP_PurchasePrice_Screen_CreateWithQuotations</flowDefinition>`
2. `force-app/main/default/quickActions/Purchase_Price__c.Update_Purchase_Price.quickAction-meta.xml`
   - `<flowDefinition>PP_PurchasePrice_Screen_UpdateByCloneAndArchive</flowDefinition>`
3. `force-app/main/default/objects/Purchase_Price__c/webLinks/Create_Purchase_Price_Wizard.webLink-meta.xml`
   - URL `/flow/PP_PurchasePrice_Screen_CreateWithQuotations`
4. Documentation `purchase-management/docs/*` (occurrences textuelles)

### 8.3 Ordre de deploiement recommande (3 phases)

#### Phase A - Renommer et deployer d'abord les Flows "providers"

Deployer uniquement les 7 nouveaux Flows (renommes), sans retirer les anciens du package au tout debut.

```bash
sf project deploy validate --source-dir force-app/main/default/flows --target-org <alias>
sf project deploy start --source-dir force-app/main/default/flows --target-org <alias>
```

Objectif: s'assurer que les nouveaux API names existent dans l'org avant bascule des references.

#### Phase B - Bascule des references (consumers)

Appliquer ensuite les changements Quick Actions + WebLink + docs, puis deploy ciblé:

```bash
sf project deploy validate \
  --source-dir force-app/main/default/quickActions \
  --source-dir force-app/main/default/objects/Purchase_Price__c/webLinks \
  --target-org <alias>

sf project deploy start \
  --source-dir force-app/main/default/quickActions \
  --source-dir force-app/main/default/objects/Purchase_Price__c/webLinks \
  --target-org <alias>
```

#### Phase C - Nettoyage des anciens noms

Une fois la recette OK, retirer les anciennes definitions de Flows du repo et deployer la suppression.

```bash
sf project deploy start --source-dir force-app/main/default/flows --target-org <alias>
```

Note: en source-tracking/scratch, preferer une suppression explicite des anciens fichiers suivie d'un deploy. En sandbox classique, verifier le comportement de delete metadata selon pipeline.

### 8.4 Sequence pratique conseillee

1. Renommer localement les 7 fichiers `*.flow-meta.xml` + `<label>` + `<interviewLabel>`.
2. Deploy Phase A.
3. Mettre a jour quick actions / weblink.
4. Deploy Phase B.
5. Test manuel:
   - Quick Action `Create Purchase Price`
   - Quick Action `Update Purchase Price`
   - bouton liste `Create Purchase Price`
   - transitions `To Confirm -> Validate` + email
6. Supprimer anciens Flows du repo.
7. Deploy Phase C.

### 8.5 Rollback rapide

Si erreur post-bascule:

1. Repoint quick actions/weblink vers anciens noms.
2. Redeployer quick actions + weblink uniquement.
3. Garder les deux generations de Flows le temps de stabiliser.

## Done / Remaining / Risks-Assumptions

### Done

- Pack pre-rempli base sur les flows existants et la doc d'implementation Purchase Price.

### Remaining

- Confirmation finale des noms DMO cibles selon ton org Data Cloud.
- Validation des filtres MCC avec l'equipe marketing.

### Risks-Assumptions

- Les noms d'objets DMO proposes sont des noms de travail (a ajuster au tenant Data Cloud reel).
- Les flows legacy `flow_*` peuvent couvrir d'autres domaines, verification fonctionnelle necessaire avant rename.


