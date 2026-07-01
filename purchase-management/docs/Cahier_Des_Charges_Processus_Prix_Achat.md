# Cahier des Charges - Processus d'Enregistrement des Prix d'Achat

## 1. Objectif du Processus

Definir un flux de travail structure pour:

- la reception du devis fournisseur,
- la verification/metier du prix,
- la validation ou negociation,
- l'enregistrement et le suivi des statuts dans Salesforce,
- la notification de cloture du processus.

Ce processus couvre les prix d'achat `Purchase_Price__c` dans Salesforce.

---

## 2. Etapes Operationnelles

### Phase 1 - Reception et saisie initiale

1. **Reception du devis** (point d'entree du processus).
2. **Archivage SharePoint** du devis dans le dossier dedie.
3. **Saisie Excel calculateur** du prix fabricant.
4. **Saisie Salesforce**:
   - creation/enregistrement du prix fabricant,
   - statut initial: `To Confirm`.

### Phase 2 - Analyse du prix

1. **Verifier si le prix est nouveau**.
2. **Si ancien prix**:
   - comparer ancien vs nouveau,
   - calculer le pourcentage d'augmentation.
3. **Si nouveau prix**:
   - demander validation a Antoine Joubin pendant le point hebdomadaire achat.

### Phase 3 - Validation et negociation

1. **Decision de validation**:
   - si valide -> appliquer les regles de statut (section 3),
   - si non valide -> lancer negociation commerciale (Antoine J. / Celine).
2. **Issue de negociation**:
   - negociation gagnee -> statut `To Ask`,
   - negociation perdue -> fin de processus ou archivage de la negociation.

---

## 3. Gestion des statuts Salesforce

## 3.1 Definition des statuts

| Statut | Description |
|---|---|
| `To Ask` | Prix existant dans l'ancienne base sans devis associe, ou prix en attente suite a negociation gagnee. |
| `To Confirm` | Prix saisi et en attente de confirmation/validation. |
| `Future Price` | Prix valide dont l'application est future. |
| `Validate` | Prix d'achat actuellement en vigueur. |
| `Obsolete` | Prix d'achat non en vigueur. |

## 3.2 Regles apres validation

### Cas A - Validite immediate

- Le nouveau prix passe en `Validate`.
- L'ancien prix (si mise a jour/remplacement) passe en `Obsolete`.

### Cas B - Validite future

- Le nouveau prix passe en `Future Price`.
- L'ancien prix reste `Validate` jusqu'a la date d'effet du nouveau prix.

---

## 4. Automatisations et notifications

## 4.1 Automatisations de statuts

Transitions automatiques obligatoires:

- `Future Price` -> `Validate` (selon Trigger Date / date d'effet),
- `Validate` -> `Obsolete` (selon regles de fin de validite/remplacement).

## 4.2 Notification sortante

Une fois le processus termine, envoyer un email a Robin contenant:

- les details du nouveau prix d'achat,
- le lien vers l'enregistrement Salesforce concerne.

## 4.3 Schema operationnel final (Creation / Update)

- **Creation**: creation du `Purchase_Price__c` -> statut `To Confirm` -> soumission approbation -> approbation -> envoi email.
- **Update**: lancement du flow d'update (clone/archive + recreation des quotations si customer-specific) -> statut `To Confirm` -> soumission approbation -> approbation -> envoi email.
- **Routing approbation**: selection automatique du process selon `RecordType` (Standard vs Customer-Specific).
- **Contenu mail creation**: message de creation de prix valide.
- **Contenu mail update**: message explicite: `Un prix d'achat a ete Mis a jour et valide.`
- **Contrainte client-specific**: passage a `To Confirm` bloque sans au moins une `Customer_s_Purchase_Quotation__c` associee.

---

## 5. Contraintes de mise en oeuvre (Salesforce)

- **Low-code first**: implementation prioritaire en Flow Salesforce.
- **No hardcoded IDs**.
- **Traçabilite**: toutes les transitions de statut doivent etre auditables.
- **Compatibilite SFDX/CLI**: livrables metadata dans `force-app/main/default/`.

---

## 6. Criteres d'acceptation

Le process est considere conforme si:

1. Un devis recu suit integralement les phases 1 -> 3.
2. Le statut initial est bien `To Confirm`.
3. Les cas "validite immediate" et "validite future" sont correctement differencies.
4. Les transitions automatiques `Future Price -> Validate` et `Validate -> Obsolete` sont actives.
5. Le mail final a Robin est envoye avec les informations attendues.

---

## 7. Usage gstack (pilotage execution)

Pour les travaux Purchase Price, gstack est utilise comme cadre d'execution dans Cursor.

- Aucune connexion externe supplementaire n'est necessaire pour l'utiliser.
- Commandes recommandees:
  - `/gstack-review` (revue avant commit/PR),
  - `/gstack-investigate` (analyse root cause en cas de bug),
  - `/gstack-qa` (verifications fonctionnelles),
  - `/gstack-ship` (preparation de livraison).
- Consigne de prompt a appliquer:
  - `Contexte Purchase Price. Applique .gbrain/.gstack. Priorite low-code (Flow) avant Apex. Apex uniquement si limitation Flow prouvee.`
- Regle de gouvernance:
  - exiger un plan de test concret et une liste des impacts objets/flows avant validation d'une implementation.

