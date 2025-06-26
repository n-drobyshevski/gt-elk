# O365 – Modifications des Règles de Messagerie – (Sécurité)

## Informations Générales

| Propriété | Valeur |
|-----------|--------|
| **Nom du Dashboard** | O365 – Modifications des Règles de Messagerie – (Sécurité) |
| **Type** | Sécurité/Security |
| **Version** | 1.0 |
| **Créé le** | 2024-12-19 |
| **Dernière mise à jour** | 2024-12-19 |
| **Équipe responsable** | Équipe Sécurité IT |
| **Contact** | security-team@organisation.fr |
| **Statut** | Actif |

## 1. Objectifs du Dashboard

### Objectif Principal

Surveiller et détecter les modifications suspectes des règles de messagerie Microsoft Exchange Online qui pourraient indiquer une compromission de compte, des tentatives de phishing avancé ou des menaces persistantes avancées (APT) ciblant la messagerie d'entreprise.

### Objectifs Spécifiques

- **Détection de règles malveillantes** : Identifier les règles automatiques créées pour rediriger ou supprimer des emails
- **Surveillance des modifications** : Traquer les changements dans les règles existantes
- **Protection contre le phishing** : Détecter les règles de transfert vers des domaines externes suspects
- **Analyse comportementale** : Identifier les utilisateurs avec des activités de création de règles anormales
- **Conformité et audit** : Maintenir une traçabilité complète des modifications de règles

## 2. Cas d'Usage

### Cas d'Usage Primaires

1. **Création de règle de transfert externe suspect**
   - *Scénario* : Un utilisateur crée une règle transférant tous les emails vers un domaine externe
   - *Action* : Alerte critique et désactivation immédiate de la règle

2. **Règle de suppression automatique**
   - *Scénario* : Création d'une règle supprimant les emails contenant "sécurité" ou "alerte"
   - *Action* : Blocage de la règle et investigation approfondie

3. **Règles créées en dehors des heures ouvrables**
   - *Scénario* : Création de règles pendant la nuit ou les week-ends
   - *Action* : Vérification de légitimité et validation utilisateur

### Cas d'Usage Secondaires

- Audit mensuel des règles de messagerie pour conformité
- Formation des utilisateurs sur les bonnes pratiques
- Analyse des tendances pour améliorer les politiques de sécurité

## 3. Sources de Données

### Sources Principales

| Source | Type | Fréquence de collecte | Volume estimé |
|--------|------|----------------------|---------------|
| **Exchange Online Audit Logs** | JSON/API | Temps réel | 1-5K événements/jour |
| **Microsoft Graph API** | REST API | 5 minutes | 500-2K événements/jour |
| **Office 365 Management API** | JSON | 15 minutes | 1-3K événements/jour |

### Sources Complémentaires

- **Azure AD Logs** : Contexte d'authentification des utilisateurs
- **Microsoft Defender for Office 365** : Données de menaces et détections
- **Threat Intelligence** : Listes de domaines malveillants

### Format des Données

```json
{
  "timestamp": "2024-12-19T14:30:00Z",
  "user_principal_name": "user@organisation.fr",
  "operation": "New-InboxRule",
  "rule_name": "Forward Important Emails",
  "rule_conditions": {
    "from": "boss@organisation.fr",
    "subject_contains": ["urgent", "confidentiel"]
  },
  "rule_actions": {
    "forward_to": "external@suspicious-domain.com",
    "delete_message": false,
    "mark_as_read": true
  },
  "client_ip": "203.0.113.1",
  "user_agent": "Microsoft Outlook 16.0"
}
```

## 4. Visualisations et Métriques

### Vue d'ensemble (Overview)

1. **Métriques clés (KPI)**

   ```text
   ┌─────────────────────┬─────────────────────┬─────────────────────┐
   │  Règles Créées 24h  │ Règles Supprimées   │   Règles Suspectes  │
   │         12          │         3           │         2           │
   └─────────────────────┴─────────────────────┴─────────────────────┘
   ```

2. **Tendance des modifications de règles**
   - *Type* : Graphique en aires empilées
   - *Période* : 30 derniers jours
   - *Séries* : Créations, modifications, suppressions

### Analyse des Règles

3. **Répartition des actions de règles**
   - *Type* : Graphique en secteurs (donut)
   - *Données* : Types d'actions (transfert, suppression, déplacement, marquage)
   - *Filtre* : Possibilité de filtrer par niveau de risque

4. **Top 10 des utilisateurs par nouvelles règles**
   - *Type* : Graphique en barres horizontales
   - *Période* : 7 derniers jours
   - *Indicateur* : Code couleur selon le niveau de risque

### Détection d'Anomalies

5. **Règles de transfert vers domaines externes**
   - *Type* : Table dynamique avec alertes
   - *Colonnes* : Utilisateur, Règle, Domaine destination, Risque, Action
   - *Tri* : Par niveau de risque décroissant

6. **Chronologie des règles suspectes**
   - *Type* : Timeline interactive
   - *Données* : 50 dernières règles marquées comme suspectes
   - *Détails* : Expansion pour voir les détails complets

### Analyse Comportementale

7. **Heatmap des créations de règles**
   - *Type* : Carte de chaleur
   - *Axes* : Jour de la semaine vs Heure
   - *Intensité* : Nombre de règles créées

8. **Distribution des domaines de transfert**
   - *Type* : Graphique en barres
   - *Données* : Top 20 des domaines externes utilisés pour le transfert
   - *Classification* : Domaines légitimes vs suspects

## 5. Configuration et Paramètres

### Règles de Classification des Risques

```yaml
risk_classification:
  critical:
    - external_forward_to_suspicious_domain
    - delete_security_emails
    - hide_inbox_rule
    
  high:
    - external_forward_all
    - delete_by_keyword
    - created_outside_hours
    
  medium:
    - internal_forward_unusual_recipient
    - complex_conditions
    - bulk_rule_creation
    
  low:
    - simple_folder_move
    - mark_as_read_only
```

### Domaines de Confiance

```yaml
trusted_domains:
  - "organisation.fr"
  - "partenaire-officiel.com"
  - "fournisseur-autorise.net"
  
suspicious_patterns:
  - ".*\\.tk$"
  - ".*\\.ml$" 
  - ".*temp.*"
  - ".*secure.*gmail\\.com"
```

### Seuils d'Alerte

```yaml
alerting_thresholds:
  rules_per_user_per_day: 5
  external_forwards_per_day: 2
  deletions_rules_per_hour: 3
  off_hours_rule_creation: 1
```

## 6. Alertes et Notifications

### Règles d'Alerte Critiques

1. **Transfert vers domaine suspect**

   ```yaml
   Condition: action.forward_to CONTAINS suspicious_domain
   Severity: Critical
   Action: Block rule + Alert RSSI
   Response_time: < 5 minutes
   ```

2. **Suppression d'emails de sécurité**

   ```yaml
   Condition: action.delete AND (subject CONTAINS "security" OR "alert")
   Severity: Critical  
   Action: Disable rule + Investigation
   Response_time: < 2 minutes
   ```

### Canaux de Notification

- **Email prioritaire** : rssi@organisation.fr, security-team@organisation.fr
- **Teams** : Canal #security-critical-alerts
- **SIEM** : Intégration directe avec Microsoft Sentinel
- **Webhook** : API vers système de ticketing

### Procédures d'Escalade

- **Niveau 1** : Analyste SOC (réponse immédiate)
- **Niveau 2** : Expert messagerie (dans les 15 minutes)
- **Niveau 3** : RSSI (dans les 30 minutes pour les critiques)

## 7. Procédures de Maintenance

### Maintenance Quotidienne

- ✅ Vérification de la connectivité aux APIs Microsoft
- ✅ Validation des règles de classification des risques
- ✅ Contrôle des faux positifs des dernières 24h
- ✅ Mise à jour des listes de domaines suspects

### Maintenance Hebdomadaire

- 🔄 Révision des seuils d'alerte basée sur les tendances
- 🔄 Analyse des nouvelles techniques d'attaque
- 🔄 Test des procédures de réponse aux incidents
- 🔄 Formation continue des équipes SOC

### Maintenance Mensuelle

- 📊 Rapport d'efficacité du dashboard
- 📊 Analyse des tendances et patterns
- 📊 Mise à jour de la base de connaissances
- 📊 Révision des procédures de réponse

### Indicateurs de Santé

```text
┌─────────────────────┬─────────────────────┬─────────────────────┐
│   Source de Données │       Statut        │    Latence          │
├─────────────────────┼─────────────────────┼─────────────────────┤
│ Exchange Audit Logs │ 🟢 Opérationnel     │ < 30s               │
│ Microsoft Graph API │ 🟢 Opérationnel     │ < 60s               │
│ Threat Intelligence │ 🟡 Dégradé          │ < 300s              │
│ Alert System        │ 🟢 Opérationnel     │ < 10s               │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

## 8. Sécurité et Conformité

### Contrôles d'Accès

- **Lecture** : Équipe sécurité, Administrateurs Exchange, SOC
- **Modification** : Administrateurs sécurité uniquement  
- **Administration** : RSSI, Administrateur principal

### Conformité Réglementaire

- **RGPD** : Anonymisation des données utilisateur après 30 jours
- **ISO 27001** : Audit trail complet de toutes les actions
- **ANSSI** : Chiffrement de bout en bout des données sensibles
- **Directive NIS** : Notification sous 24h des incidents critiques

### Audit et Traçabilité

- Historique complet des accès et modifications
- Signature numérique des rapports d'incident
- Sauvegarde chiffrée quotidienne des configurations
- Journal d'audit conservé pendant 7 ans

### Protection des Données

```yaml
data_protection:
  encryption_at_rest: "AES-256-GCM"
  encryption_in_transit: "TLS 1.3"
  data_classification: "Confidentiel Défense"
  retention_policy: "3 ans active + 4 ans archive"
  anonymization_delay: "30 jours"
```

## 9. Performance et Optimisation

### Métriques de Performance

- **Temps de détection** : < 2 minutes
- **Temps de réponse aux alertes** : < 5 minutes  
- **Taux de faux positifs** : < 2%
- **Disponibilité** : 99.95%

### Optimisations Techniques

- Cache intelligent pour les requêtes fréquentes
- Index optimisés sur les champs de recherche
- Compression des données historiques
- Load balancing des requêtes API

## 10. Documentation Technique

### Prérequis

- Microsoft 365 E5 ou équivalent avec licence Defender
- Permissions d'audit Exchange Online
- Accès API Microsoft Graph avec scopes appropriés
- Certificats de sécurité valides

### Scripts de Déploiement

```bash
# Configuration initiale
./setup_exchange_monitoring.ps1

# Déploiement du dashboard  
./deploy_o365_email_rules.sh

# Configuration des alertes
./configure_email_alerts.ps1
```

### Dépannage

| Problème | Cause | Solution |
|----------|-------|----------|
| Données manquantes | Token API expiré | Renouveler les credentials |
| Faux positifs | Seuils trop bas | Ajuster la configuration |
| Alertes manquées | Webhook défaillant | Vérifier la connectivité |

---

## Annexes

### A. Classification des Actions de Règles

| Action | Niveau de Risque | Description |
|--------|-----------------|-------------|
| ForwardTo (externe) | 🔴 Critique | Transfert vers domaine externe |
| DeleteMessage | 🔴 Critique | Suppression automatique |
| MoveToFolder | 🟡 Moyen | Déplacement vers dossier |
| MarkAsRead | 🟢 Faible | Marquage comme lu |

### B. Patterns de Détection

```regex
# Règles suspectes communes
suspicious_rule_names:
  - ".*temp.*"
  - ".*backup.*"
  - ".*security.*"
  - "^\\.$"  # Règle avec nom vide/point
```

### C. Références Techniques

- [Exchange Online PowerShell](https://docs.microsoft.com/powershell/exchange/)
- [Microsoft Graph API Audit Logs](https://docs.microsoft.com/graph/api/resources/auditlogrecord)
- [Defender for Office 365 Docs](https://docs.microsoft.com/microsoft-365/security/office-365-security/)

---

**Dernière révision** : 2024-12-19  
**Prochaine révision** : 2025-01-19  
**Responsable** : Équipe Sécurité IT
