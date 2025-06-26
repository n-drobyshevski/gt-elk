# O365 – Activités Fichiers & Permissions – (Protection des Données)

## Informations Générales

| Propriété | Valeur |
|-----------|--------|
| **Nom du Dashboard** | O365 – Activités Fichiers & Permissions – (Protection des Données) |
| **Type** | Protection des Données/Data Protection |
| **Version** | 1.0 |
| **Créé le** | 2024-12-19 |
| **Dernière mise à jour** | 2024-12-19 |
| **Équipe responsable** | Équipe Sécurité IT & Data Protection |
| **Contact** | data-protection@organisation.fr |
| **Statut** | Actif |

## 1. Objectifs du Dashboard

### Objectif Principal

Surveiller et analyser les activités sur les fichiers dans l'écosystème Microsoft 365 (SharePoint Online, OneDrive for Business, Microsoft Teams) pour détecter les accès non autorisés, les fuites de données, les modifications suspectes et assurer la conformité avec les politiques de protection des données.

### Objectifs Spécifiques

- **Surveillance des accès** : Monitorer qui accède à quels fichiers et quand
- **Détection de fuites** : Identifier les partages externes suspects ou non autorisés
- **Analyse des permissions** : Traquer les modifications de permissions et privilèges
- **Protection des données sensibles** : Surveiller l'accès aux fichiers classifiés
- **Conformité RGPD** : Assurer la traçabilité des accès aux données personnelles
- **Détection d'exfiltration** : Identifier les téléchargements massifs ou suspects

## 2. Cas d'Usage

### Cas d'Usage Primaires

1. **Téléchargement massif de fichiers**
   - *Scénario* : Un utilisateur télécharge plus de 100 fichiers en moins d'une heure
   - *Action* : Alerte immédiate et blocage temporaire de l'accès

2. **Partage externe non autorisé**
   - *Scénario* : Fichier confidentiel partagé avec un domaine externe non approuvé
   - *Action* : Révocation immédiate du partage et notification RSSI

3. **Accès depuis une localisation inhabituelle**
   - *Scénario* : Accès à des fichiers sensibles depuis un pays non autorisé
   - *Action* : Suspension de l'accès et vérification d'identité

4. **Modification de permissions élevées**
   - *Scénario* : Attribution de permissions "Propriétaire" à un utilisateur externe
   - *Action* : Révocation automatique et escalade au DPO

### Cas d'Usage Secondaires

- Audit trimestriel des accès aux données personnelles (RGPD)
- Analyse des patterns d'utilisation pour optimiser les performances
- Investigation d'incidents de sécurité post-breach
- Reporting de conformité pour audits réglementaires

## 3. Sources de Données

### Sources Principales

| Source | Type | Fréquence de collecte | Volume estimé |
|--------|------|----------------------|---------------|
| **SharePoint Online Logs** | JSON/API | Temps réel | 50-200K événements/jour |
| **OneDrive Activity Logs** | REST API | 30 secondes | 20-100K événements/jour |
| **Microsoft Teams Files** | Graph API | 2 minutes | 10-50K événements/jour |
| **Microsoft Cloud App Security** | API | 5 minutes | 5-25K événements/jour |

### Sources Complémentaires

- **Azure Information Protection** : Classification et étiquetage des fichiers
- **Microsoft Purview** : Données de conformité et gouvernance
- **Exchange Online** : Pièces jointes dans les emails
- **Threat Intelligence** : Indicateurs de compromission liés aux fichiers

### Format des Données

```json
{
  "timestamp": "2024-12-19T15:45:00Z",
  "user_principal_name": "user@organisation.fr",
  "operation": "FileDownloaded",
  "workload": "SharePoint",
  "object_id": "https://tenant.sharepoint.com/sites/finance/documents/budget_2024.xlsx",
  "file_name": "budget_2024.xlsx",
  "file_extension": "xlsx",
  "file_size_bytes": 2048576,
  "site_url": "https://tenant.sharepoint.com/sites/finance",
  "sensitivity_label": "Confidentiel",
  "client_ip": "203.0.113.1",
  "user_agent": "Microsoft Office Excel 2021",
  "access_method": "Web browser",
  "sharing_scope": "Internal",
  "permission_level": "Read"
}
```

## 4. Visualisations et Métriques

### Vue d'ensemble (Overview)

1. **Métriques clés (KPI)**

   ```text
   ┌─────────────────────┬─────────────────────┬─────────────────────┐
   │  Fichiers Accédés   │  Téléchargements    │   Partages Externes │
   │       25,847        │       1,234         │         23          │
   └─────────────────────┴─────────────────────┴─────────────────────┘
   ```

2. **Tendances d'activité fichiers**
   - *Type* : Graphique en aires empilées
   - *Période* : 30 derniers jours
   - *Séries* : Accès, téléchargements, modifications, partages

### Analyse des Activités

3. **Répartition des opérations sur fichiers**
   - *Type* : Graphique en secteurs avec drill-down
   - *Données* : FileViewed, FileDownloaded, FileModified, FileShared, FileDeleted
   - *Filtres* : Par type de fichier, sensibilité, localisation

4. **Top 20 des fichiers les plus accédés**
   - *Type* : Table dynamique avec indicateurs de risque
   - *Colonnes* : Nom fichier, Classification, Nb accès, Dernière activité, Niveau risque
   - *Tri* : Par nombre d'accès décroissant

### Surveillance des Accès

5. **Heatmap des accès par heure et jour**
   - *Type* : Carte de chaleur interactive
   - *Axes* : Jour de la semaine vs Heure de la journée
   - *Intensité* : Volume d'accès aux fichiers sensibles

6. **Top 10 des utilisateurs par volume d'activité**
   - *Type* : Graphique en barres avec breakdown par type d'opération
   - *Période* : 7 derniers jours
   - *Indicateurs* : Code couleur selon le niveau de risque comportemental

### Détection d'Anomalies

7. **Téléchargements massifs détectés**
   - *Type* : Table d'alertes avec timeline
   - *Colonnes* : Utilisateur, Nb fichiers, Taille totale, Période, Statut investigation
   - *Seuils** : Configurable (défaut: >50 fichiers/heure)

8. **Partages externes suspects**
   - *Type* : Liste avec géolocalisation
   - *Données* : Fichier, Destinataire externe, Domaine, Pays, Classification
   - *Alertes* : Partage de fichiers "Confidentiel" vers l'externe

### Analyse des Permissions

9. **Évolution des permissions de partage**
   - *Type* : Graphique en aires
   - *Séries* : Privé, Interne, Externe autorisé, Externe public
   - *Période* : 90 derniers jours

10. **Modifications de permissions critiques**
    - *Type* : Timeline des événements
    - *Focus* : Élévation de privilèges, accès externe, permissions sensibles
    - *Détails* : Qui, quoi, quand, pourquoi (si disponible)

## 5. Configuration et Paramètres

### Classification des Fichiers par Sensibilité

```yaml
sensitivity_levels:
  public:
    label: "Public"
    risk_level: 1
    monitoring: "Light"
    
  internal:
    label: "Usage Interne"
    risk_level: 2
    monitoring: "Standard"
    
  confidential:
    label: "Confidentiel"
    risk_level: 4
    monitoring: "Enhanced"
    
  secret:
    label: "Secret Défense"
    risk_level: 5
    monitoring: "Maximum"
```

### Seuils d'Alerte par Type d'Activité

```yaml
alert_thresholds:
  mass_download:
    files_per_hour: 50
    total_size_mb: 1000
    
  external_sharing:
    confidential_files: 1
    public_files: 10
    
  unusual_access:
    access_outside_hours: 5
    foreign_country_access: 1
    
  permission_changes:
    privilege_escalation: 1
    external_permissions: 1
```

### Extensions de Fichiers Surveillées

```yaml
monitored_extensions:
  high_risk:
    - ".xlsx"  # Fichiers Excel (données financières)
    - ".docx"  # Documents Word (confidentiels)
    - ".pdf"   # Documents PDF (contractuels)
    - ".pst"   # Archives Outlook
    
  medium_risk:
    - ".pptx"  # Présentations
    - ".zip"   # Archives
    - ".csv"   # Données tabulaires
    
  executable_risk:
    - ".exe"   # Exécutables
    - ".msi"   # Installeurs
    - ".bat"   # Scripts batch
```

## 6. Alertes et Notifications

### Règles d'Alerte Critiques

1. **Exfiltration massive détectée**

   ```yaml
   Condition: downloads > 100 files OR total_size > 5GB IN 1 hour
   Severity: Critical
   Action: Block user + Alert RSSI + Forensic capture
   Response_time: < 2 minutes
   ```

2. **Partage de données classifiées**

   ```yaml
   Condition: sensitivity_label = "Secret" AND sharing_scope = "External"
   Severity: Critical
   Action: Revoke sharing + Alert DPO + Investigation
   Response_time: < 1 minute
   ```

3. **Accès depuis pays interdit**

   ```yaml
   Condition: file_access AND country IN blacklist AND sensitivity >= "Confidential"
   Severity: High
   Action: Block access + User verification + Log analysis
   Response_time: < 5 minutes
   ```

### Canaux de Notification

- **Email urgent** : rssi@organisation.fr, dpo@organisation.fr
- **Teams** : Canal #data-protection-alerts
- **SIEM** : Intégration avec Microsoft Sentinel et SOAR
- **SMS** : Équipe d'astreinte pour les critiques uniquement

### Matrices d'Escalade

| Niveau de Sensibilité | Temps de Réponse | Équipe Notifiée |
|----------------------|------------------|-----------------|
| Public | 4 heures | Équipe IT |
| Interne | 2 heures | Security Team |
| Confidentiel | 30 minutes | RSSI + DPO |
| Secret Défense | 5 minutes | RSSI + Direction + DPO |

## 7. Procédures de Maintenance

### Maintenance Quotidienne

- ✅ Vérification de la connectivité aux APIs Microsoft 365
- ✅ Contrôle de la fraîcheur des données (latence < 5 minutes)
- ✅ Validation des seuils d'alerte et ajustement si nécessaire
- ✅ Analyse des faux positifs des dernières 24 heures

### Maintenance Hebdomadaire

- 🔄 Mise à jour des listes de domaines autorisés/interdits
- 🔄 Révision des classifications de sensibilité des fichiers
- 🔄 Test des procédures de réponse aux incidents
- 🔄 Formation continue des équipes de surveillance

### Maintenance Mensuelle

- 📊 Analyse des tendances et ajustement des modèles de détection
- 📊 Rapport d'efficacité et recommandations d'amélioration
- 📊 Mise à jour de la documentation et procédures
- 📊 Audit des accès au dashboard et révision des permissions

### Monitoring de Santé du Système

```text
┌─────────────────────┬─────────────────────┬─────────────────────┐
│   Source de Données │       Statut        │    Dernière MAJ     │
├─────────────────────┼─────────────────────┼─────────────────────┤
│ SharePoint Online   │ 🟢 Opérationnel     │ 1min ago            │
│ OneDrive Business   │ 🟢 Opérationnel     │ 2min ago            │
│ Teams Files         │ 🟡 Dégradé          │ 8min ago            │
│ Cloud App Security  │ 🟢 Opérationnel     │ 3min ago            │
│ Azure IP Protection │ 🟢 Opérationnel     │ 5min ago            │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

## 8. Sécurité et Conformité

### Contrôles d'Accès Granulaires

- **Lecture globale** : Équipe sécurité, DPO, Auditeurs internes
- **Lecture limitée** : Responsables métier (données de leur périmètre uniquement)
- **Modification** : Administrateurs Data Protection uniquement
- **Administration** : RSSI, DPO, Administrateur principal système

### Conformité Réglementaire Spécifique

- **RGPD (Articles 32, 33, 34)** : Notification des violations < 72h, registre des traitements
- **ISO 27001** : Contrôles A.12.6 (Gestion des vulnérabilités techniques)
- **ANSSI** : Conformité au RGS (Référentiel Général de Sécurité)
- **SOX** : Contrôles sur l'accès aux données financières sensibles
- **HIPAA** : Protection des données de santé (si applicable)

### Audit et Traçabilité Renforcée

- **Journalisation complète** : Tous les accès au dashboard avec horodatage
- **Intégrité des données** : Signature numérique et hashage des logs
- **Conservation** : 7 ans pour données financières, 5 ans pour autres
- **Anonymisation** : RGPD-compliant après expiration des délais légaux

### Chiffrement et Protection

```yaml
data_protection_measures:
  encryption_at_rest: "AES-256-GCM"
  encryption_in_transit: "TLS 1.3 avec Perfect Forward Secrecy"
  key_management: "Azure Key Vault avec HSM"
  data_masking: "Automatic pour données personnelles"
  backup_encryption: "Separate key per backup set"
  geographical_restriction: "EU-only data residency"
```

## 9. Performance et Optimisation

### Métriques de Performance Cibles

- **Latence de détection** : < 3 minutes pour événements critiques
- **Temps de chargement dashboard** : < 5 secondes
- **Débit de traitement** : 10,000 événements/minute
- **Disponibilité** : 99.9% (SLA)
- **Précision détection** : > 95% (taux de vrais positifs)

### Optimisations Techniques Implementées

- **Indexation intelligente** : Index composites sur (user, timestamp, sensitivity)
- **Cache distribué** : Redis Cluster pour les requêtes fréquentes
- **Pré-agrégation** : Calculs pré-faits pour métriques courantes
- **Compression** : LZ4 pour données historiques > 30 jours
- **Partitioning** : Données partitionnées par mois pour performance

### Monitoring des Performances

```yaml
performance_metrics:
  api_response_time:
    target: "< 500ms"
    critical_threshold: "> 2s"
    
  data_freshness:
    target: "< 5min"
    critical_threshold: "> 15min"
    
  alert_processing:
    target: "< 30s"
    critical_threshold: "> 2min"
```

## 10. Documentation Technique

### Prérequis Techniques Détaillés

- **Microsoft 365** : E5 ou E3 + Compliance & Security add-ons
- **Licenses requises** : Cloud App Security, Azure Information Protection P2
- **APIs activées** : Microsoft Graph (Files.Read.All, Sites.Read.All, User.Read.All)
- **Permissions Azure** : Security Reader, Compliance Administrator
- **Infrastructure** : Elasticsearch 8.x, Kibana 8.x, Logstash 8.x

### Scripts de Déploiement et Configuration

```bash
# Configuration initiale M365
./setup_m365_file_monitoring.ps1

# Déploiement du dashboard Kibana
./deploy_file_activity_dashboard.sh

# Configuration des connecteurs
./configure_sharepoint_connector.py
./configure_onedrive_connector.py

# Import des règles de détection
./import_detection_rules.sh file_activity_rules.ndjson
```

### Diagnostic et Dépannage

| Symptôme | Cause Probable | Diagnostic | Solution |
|----------|---------------|------------|----------|
| Données manquantes | Token API expiré | Check logs auth | Renouveler credentials |
| Latence élevée | Charge API trop élevée | Monitor throttling | Implémenter retry logic |
| Faux positifs | Seuils mal calibrés | Analyse historique | Ajuster configuration |
| Alertes manquées | Webhook défaillant | Test connectivity | Reconfigurer endpoints |

### Scripts de Maintenance Automatisée

```bash
# Nettoyage automatique des anciennes données
0 2 * * * /scripts/cleanup_old_data.sh

# Mise à jour des listes de domaines suspects
0 6 * * * /scripts/update_threat_intel.py

# Vérification santé du système
*/15 * * * * /scripts/health_check.sh

# Rapport quotidien automatique
0 8 * * 1-5 /scripts/daily_report.py
```

---

## Annexes

### A. Classification des Types de Fichiers

| Type | Extensions | Niveau de Surveillance | Retention |
|------|------------|----------------------|-----------|
| Documents Office | .docx, .xlsx, .pptx | Élevé | 7 ans |
| PDFs | .pdf | Élevé | 7 ans |
| Archives | .zip, .rar, .7z | Moyen | 3 ans |
| Médias | .jpg, .mp4, .mp3 | Faible | 1 an |
| Code source | .py, .js, .java | Élevé | 5 ans |

### B. Matrice des Risques

```text
                    Impact
                 Low  Med  High Critical
Probability  Low   1    2    3      4
            Med    2    3    4      5  
           High    3    4    5      5
       Critical    4    5    5      5
```

### C. Références et Standards

- [Microsoft 365 Activity APIs](https://docs.microsoft.com/office/office-365-management-api/)
- [RGPD - Texte officiel](https://eur-lex.europa.eu/eli/reg/2016/679/oj)
- [ANSSI - Guide d'hygiène informatique](https://www.ssi.gouv.fr/guide/guide-dhygiene-informatique/)
- [ISO 27001:2022](https://www.iso.org/standard/82875.html)

### D. Changelog et Versioning

| Version | Date | Modifications | Auteur |
|---------|------|---------------|--------|
| 1.0 | 2024-12-19 | Création initiale du dashboard | Équipe Data Protection |
| 1.1 | À venir | Intégration Azure Purview | Planifié Q1 2025 |

---

**Dernière révision** : 2024-12-19  
**Prochaine révision** : 2025-01-19  
**Responsable** : Équipe Data Protection & Sécurité IT  
**Validé par** : DPO, RSSI
