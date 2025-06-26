# O365 – Activités de Connexion Suspectes – (Surveillance)

## Informations Générales

| Propriété | Valeur |
|-----------|--------|
| **Nom du Dashboard** | O365 – Activités de Connexion Suspectes – (Surveillance) |
| **Type** | Surveillance/Monitoring |
| **Version** | 1.0 |
| **Créé le** | 2024-12-19 |
| **Dernière mise à jour** | 2024-12-19 |
| **Équipe responsable** | Équipe Sécurité IT |
| **Contact** | security-team@organisation.fr |
| **Statut** | Actif |

## 1. Objectifs du Dashboard

### Objectif Principal
Détecter et surveiller les activités de connexion suspectes sur la plateforme Microsoft O365 en analysant les modèles de connexion géographiques, temporels et comportementaux pour identifier les tentatives d'accès non autorisées ou compromissions de comptes.

### Objectifs Spécifiques
- **Surveillance géographique** : Identifier les connexions depuis des pays non autorisés ou inhabituels
- **Détection d'anomalies** : Repérer les connexions simultanées depuis plusieurs localisations
- **Analyse temporelle** : Surveiller les connexions en dehors des heures de travail
- **Protection des comptes** : Détecter les tentatives de connexion par force brute
- **Conformité réglementaire** : Assurer la traçabilité des accès pour les audits de sécurité

## 2. Cas d'Usage

### Cas d'Usage Primaires
1. **Connexion depuis un pays non autorisé**
   - *Scénario* : Un utilisateur se connecte depuis un pays figurant sur la liste noire
   - *Action* : Alerte immédiate et suspension temporaire du compte

2. **Connexions simultanées impossibles**
   - *Scénario* : Connexions depuis deux continents différents dans un délai de 30 minutes
   - *Action* : Notification à l'équipe sécurité et demande de vérification utilisateur

3. **Connexions en dehors des heures ouvrables**
   - *Scénario* : Connexions répétées entre 22h et 6h du matin
   - *Action* : Surveillance renforcée et vérification de légitimité

### Cas d'Usage Secondaires
- Analyse des tendances de connexion pour optimiser les politiques d'accès
- Support à l'investigation d'incidents de sécurité
- Reporting mensuel des activités de connexion pour la direction

## 3. Sources de Données

### Sources Principales
| Source | Type | Fréquence de collecte | Volume estimé |
|--------|------|----------------------|---------------|
| **Azure AD Sign-in Logs** | JSON/API | Temps réel | 10-50K événements/jour |
| **Microsoft Cloud App Security** | API | 15 minutes | 5-20K événements/jour |
| **Office 365 Activity Logs** | REST API | 5 minutes | 20-100K événements/jour |

### Sources Complémentaires
- **GeoIP Database** : MaxMind GeoLite2 pour la géolocalisation des adresses IP
- **Threat Intelligence Feeds** : Listes d'adresses IP malveillantes
- **Active Directory** : Informations sur les utilisateurs et groupes

### Format des Données
```json
{
  "timestamp": "2024-12-19T10:30:00Z",
  "user_principal_name": "user@organisation.fr",
  "ip_address": "203.0.113.1",
  "country": "France",
  "city": "Paris",
  "device_info": "Windows 10, Chrome 91",
  "application": "Microsoft Office 365",
  "result_type": "Success",
  "risk_level": "Low"
}
```

## 4. Visualisations et Métriques

### Vue d'ensemble (Overview)
1. **Carte mondiale des connexions**
   - *Type* : Carte géographique interactive
   - *Données* : Nombre de connexions par pays sur les 24 dernières heures
   - *Couleurs* : Vert (autorisé), Orange (surveillance), Rouge (interdit)

2. **Métriques clés (KPI)**
   ```
   ┌─────────────────────┬─────────────────────┬─────────────────────┐
   │  Connexions Totales │ Connexions Suspectes│    Pays Uniques     │
   │      15,847         │        23           │         45          │
   └─────────────────────┴─────────────────────┴─────────────────────┘
   ```

### Détection d'Anomalies
3. **Top 10 des pays par connexions**
   - *Type* : Graphique en barres horizontales
   - *Période* : 7 derniers jours
   - *Filtre* : Possibilité d'exclure les pays autorisés

4. **Connexions depuis des emplacements inhabituels**
   - *Type* : Table dynamique
   - *Colonnes* : Utilisateur, Pays, Ville, IP, Heure, Statut risque
   - *Mise à jour* : Temps réel

### Analyse Temporelle
5. **Tendances des connexions par heure**
   - *Type* : Graphique en aires empilées
   - *Axe X* : Heures (0-23)
   - *Axe Y* : Nombre de connexions
   - *Séries* : Connexions normales vs suspectes

6. **Heatmap des connexions hebdomadaires**
   - *Type* : Carte de chaleur
   - *Axes* : Jour de la semaine vs Heure de la journée
   - *Intensité* : Nombre de connexions suspectes

### Détails des Utilisateurs
7. **Top 10 des utilisateurs par connexions suspectes**
   - *Type* : Table avec graphiques sparkline
   - *Colonnes* : Nom, Département, Nombre d'alertes, Dernière connexion suspecte

8. **Chronologie des événements suspects**
   - *Type* : Timeline interactive
   - *Données* : Dernières 50 connexions suspectes
   - *Détails* : Clic pour voir les détails complets

## 5. Configuration et Paramètres

### Seuils d'Alerte
```yaml
alerting_thresholds:
  high_risk_countries:
    - "CN"  # Chine
    - "RU"  # Russie
    - "KP"  # Corée du Nord
    
  medium_risk_indicators:
    impossible_travel_time: "4 hours"
    unusual_location_threshold: "100 km"
    off_hours_start: "20:00"
    off_hours_end: "06:00"
    
  volume_thresholds:
    suspicious_connections_per_user_per_day: 5
    failed_login_attempts_threshold: 10
```

### Filtres Disponibles
- **Période** : 1h, 24h, 7j, 30j, personnalisée
- **Utilisateurs** : Individuel, groupe, département
- **Géographie** : Pays, région, ville
- **Type de risque** : Faible, moyen, élevé, critique
- **Application** : Exchange, SharePoint, Teams, etc.

### Paramètres de Rafraîchissement
- **Données temps réel** : 30 secondes
- **Données historiques** : 5 minutes
- **Données GeoIP** : 24 heures
- **Threat Intelligence** : 1 heure

## 6. Alertes et Notifications

### Règles d'Alerte Critiques
1. **Connexion depuis un pays interdit**
   ```
   Condition: country IN ["CN", "RU", "KP", "IR"]
   Action: Alerte immédiate + SMS
   Destinataires: Équipe sécurité + Manager utilisateur
   ```

2. **Voyage impossible détecté**
   ```
   Condition: distance > 1000km AND time_diff < 2h
   Action: Alerte haute priorité
   Destinataires: SOC + Équipe sécurité
   ```

### Canaux de Notification
- **Email** : security-alerts@organisation.fr
- **Teams** : Canal #security-alerts
- **SIEM** : Intégration avec Sentinel
- **SMS** : Équipe d'astreinte (critiques uniquement)

### Escalade
- **Niveau 1** : Analyste SOC (0-15 minutes)
- **Niveau 2** : Responsable sécurité (15-30 minutes)
- **Niveau 3** : RSSI (30+ minutes)

## 7. Procédures de Maintenance

### Maintenance Quotidienne
- ✅ Vérification du statut des sources de données
- ✅ Contrôle de la fraîcheur des données GeoIP
- ✅ Validation des seuils d'alerte
- ✅ Analyse des faux positifs

### Maintenance Hebdomadaire
- 🔄 Mise à jour des listes de pays autorisés/interdits
- 🔄 Révision des règles d'alerte
- 🔄 Nettoyage des données obsolètes (>90 jours)
- 🔄 Test des canaux de notification

### Maintenance Mensuelle
- 📊 Analyse des tendances et ajustement des seuils
- 📊 Rapport de performance du dashboard
- 📊 Mise à jour de la documentation
- 📊 Formation/sensibilisation des équipes

### Tableau de Bord de Santé
```
┌─────────────────────┬─────────────────────┬─────────────────────┐
│   Source de Données │       Statut        │  Dernière MAJ       │
├─────────────────────┼─────────────────────┼─────────────────────┤
│ Azure AD Logs       │ 🟢 Opérationnel     │ 2min ago            │
│ Cloud App Security  │ 🟢 Opérationnel     │ 5min ago            │
│ GeoIP Database      │ 🟢 Opérationnel     │ 2h ago              │
│ Threat Intel        │ 🟡 Dégradé          │ 45min ago           │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

## 8. Sécurité et Conformité

### Contrôles d'Accès
- **Lecture** : Équipe sécurité, SOC, Management IT
- **Modification** : Administrateurs sécurité uniquement
- **Administration** : RSSI, Administrateur Système

### Conformité Réglementaire
- **RGPD** : Pseudonymisation des données personnelles après 30 jours
- **ISO 27001** : Logging de tous les accès au dashboard
- **ANSSI** : Chiffrement des données en transit et au repos
- **SOX** : Conservation des logs d'audit pendant 7 ans

### Audit et Traçabilité
- Tous les accès au dashboard sont loggés
- Historique des modifications de configuration conservé
- Export automatique mensuel pour archivage
- Signature numérique des rapports critiques

### Protection des Données
```yaml
data_protection:
  encryption_at_rest: "AES-256"
  encryption_in_transit: "TLS 1.3"
  data_retention: "90 days"
  anonymization_after: "30 days"
  backup_frequency: "Daily"
  backup_retention: "1 year"
```

## 9. Performance et Optimisation

### Métriques de Performance
- **Temps de chargement** : < 3 secondes
- **Temps de requête** : < 1 seconde
- **Disponibilité** : 99.9%
- **Débit** : 1000 requêtes/minute

### Optimisations Implementées
- Index sur les champs de recherche fréquents
- Cache Redis pour les requêtes géographiques
- Agrégation pré-calculée pour les métriques horaires
- Compression des données historiques

## 10. Documentation Technique

### Prérequis Techniques
- Elasticsearch 7.x ou supérieur
- Kibana 7.x ou supérieur
- Connecteur Microsoft Graph API
- Base de données GeoIP MaxMind
- Certificats TLS valides pour les APIs Microsoft

### Scripts de Déploiement
```bash
# Déploiement du dashboard
./deploy_o365_suspicious_logins.sh

# Configuration des index patterns
./configure_index_patterns.sh o365-signin-*

# Import des visualisations
./import_visualizations.sh o365_suspicious_logins.ndjson
```

### Dépannage Courant
| Problème | Cause Probable | Solution |
|----------|---------------|----------|
| Données manquantes | API Microsoft indisponible | Vérifier la connectivité et les tokens |
| Géolocalisation incorrecte | Base GeoIP obsolète | Mettre à jour MaxMind GeoLite2 |
| Faux positifs | Seuils trop restrictifs | Ajuster les paramètres d'alerte |

---

## Annexes

### A. Glossaire Technique
- **Azure AD** : Azure Active Directory, service d'identité Microsoft
- **MCAS** : Microsoft Cloud App Security
- **GeoIP** : Géolocalisation basée sur l'adresse IP
- **IOC** : Indicator of Compromise

### B. Références
- [Microsoft Graph API Documentation](https://docs.microsoft.com/en-us/graph/)
- [Azure AD Sign-in Logs Schema](https://docs.microsoft.com/en-us/azure/active-directory/reports-monitoring/)
- [ANSSI Guide Sécurité O365](https://www.ssi.gouv.fr/)

### C. Changelog
| Version | Date | Modifications |
|---------|------|---------------|
| 1.0 | 2024-12-19 | Création initiale du dashboard |

---
**Dernière révision** : 2024-12-19  
**Prochaine révision** : 2025-01-19  
**Responsable** : Équipe Sécurité IT
