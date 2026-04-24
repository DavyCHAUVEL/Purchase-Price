# Delta - CDC vs Implementation (Purchase Price)

Perimetre compare:

- CDC: `Cahier_Des_Charges_Processus_Prix_Achat.md`
- Implementation: `Purchase_Price_Automation_Implementation_Documentation.md`

---

## 1) Matrice de conformite

| Exigence CDC | Statut | Commentaire |
|---|---|---|
| Saisie initiale Salesforce en `To Confirm` | ✅ Conforme | Couvert via wizard + flow d'auto-soumission. |
| Validation approbateur Antoine/Celine | ✅ Conforme | Approval process `Purchase_Price_Validation` en place. |
| Si valide: mise a jour statuts immediate/future | ✅ Conforme | `Validate` / `Future Price` selon date, obsolete de l'ancien prix geree. |
| Transition auto `Future Price -> Validate` | ✅ Conforme | Couvert par flow schedule lifecycle. |
| Transition auto `Validate -> Obsolete` | ✅ Conforme | Couvert par regles de lifecycle/remplacement. |
| Notification Robin en fin de processus | ⚠️ Partiel | Email OK, mais destinataire actuel = user de test sandbox (pas Robin). |
| Comparatif ancien/nouveau + % augmentation | ❌ Manquant | Pas de calcul explicite du pourcentage dans l'implementation documentee. |
| Regle metier complete "nouveau vs update" avec loop dedupe et quotations | ⚠️ Partiel | Dedupe standard fait; dedupe specifique (set clients) non livre. |
| Negociation perdue: fin process/archivage | ❌ Manquant | Reject actuel force `To Ask`; pas de branche "negociation perdue" formelle. |
| Integrations operatoires SharePoint + Excel | ❌ Hors metadata / Manquant | Mention process, pas d'automatisation Salesforce livree. |

---

## 2) Ecarts prioritaires a corriger

## P1 - Critiques metier

1. **Destinataire Robin non aligne**
   - Ecart: flow notification pointe user sandbox de test.
   - Impact: process "termine" non notifie au bon destinataire.
   - Correction Flow:
     - remplacer lookup destinataire par Robin (ou DL metier),
     - conserver sender Org-Wide.

2. **Comparatif prix + % augmentation absent**
   - Ecart: CDC demande comparaison ancien vs nouveau pour ancien prix.
   - Impact: manque d'aide a decision en validation.
   - Correction Low-code:
     - ajouter formules/fields de comparaison sur wizard update,
     - exposer `% increase` dans ecran + email recap.

## P2 - Importants

3. **Dedupe prix specifiques incomplet**
   - Ecart: uniquement dedupe standard; set de clients non compare.
   - Impact: risque de doublons sur prix client-specifiques.
   - Correction:
     - sous-flow de normalisation des quotations clients (tri + concat),
     - comparaison empreinte + signature clients avant create.

4. **Branche negociation perdue non explicite**
   - Ecart: reject -> `To Ask` uniquement.
   - Impact: comportement potentiellement contradictoire avec CDC.
   - Correction:
     - decision metier a figer: `To Ask` vs `Archive/Close`,
     - implémenter une issue explicite dans approval/rework flow.

## P3 - Complementaires

5. **Raccord process SharePoint/Excel**
   - Ecart: hors metadata Salesforce actuel.
   - Impact: traçabilite transverse partielle.
   - Correction:
     - minimum: champs de trace (`Quote_File_URL__c`, `Calculator_Ref__c`),
     - option: integration Power Automate / webhook.

---

## 3) Actions Flow precises (No-Apex)

1. `PP_PurchasePrice_AfterSave_SendApprovalNotification`
   - remplacer `Get_Robin_User` par lookup Robin/DL metier production-safe.

2. `PP_PurchasePrice_Screen_UpdateByCloneAndArchive`
   - ajouter formule `%Increase = (NewPrice - OldPrice) / OldPrice`,
   - afficher resultat sur ecran de confirmation,
   - passer valeur en champ technique ou texte email.

3. `PP_PurchasePrice_BeforeSave_BlockDuplicateStandard`
   - extraire logique en sous-flow "BuildCustomerSetSignature",
   - etendre controle pour prix specifiques (empreinte + signature clients).

4. Approval / post-reject path
   - ajouter decision explicite "negociation perdue" (archive/end),
   - documenter statut final cible.

---

## 4) Decision metier a trancher (bloquants)

1. En cas de rejet approval: statut final souhaite?
   - Option A: `To Ask` (actuel)
   - Option B: `Archive/Closed`
2. "Negociation perdue => Validate" du texte historique est-il abandonne officiellement?
3. Notification finale: Robin individuel ou distribution list?

---

## 5) Resume executif

- **Conforme**: coeur workflow Salesforce (approval, transitions lifecycle, notifications structurees).
- **Partiel**: dedupe avancee et destinataire final de notification.
- **Manquant**: comparatif % augmentation, branche negociation perdue formalisee, raccord operatoire SharePoint/Excel.

