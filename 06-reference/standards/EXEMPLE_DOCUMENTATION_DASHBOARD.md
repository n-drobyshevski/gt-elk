# O365 – Email Security – (Phishing Detection)

## Informations Générales

**Nom**: `O365 – Email Security – (Phishing Detection)`  
**Version**: 1.0  
**Plateforme**: O365  
**Catégorie**: Security  
**Tags**: o365, security, email, phishing, threat-detection

## 📋 Description

### Objectif
Dashboard de détection et d'analyse des tentatives de phishing dans l'environnement Office 365, permettant une détection précoce et une réponse rapide aux menaces par email.

### Cas d'Usage
- Détection en temps réel des emails suspects
- Analyse des patterns d'attaque de phishing
- Investigation des incidents de sécurité email
- Suivi des campagnes de phishing actives

## 🔍 Sources de Données

### Index Patterns
```yaml
principal: "o365-email-security-*"
exchange: "o365-exchange-*"
threat: "threat-intel-phishing-*"
```

### Champs Critiques
```yaml
- email.sender:
    description: "Adresse email de l'expéditeur"
    type: "keyword"
    exemple: "suspicious@domain.com"
- email.subject:
    description: "Objet du message"
    type: "text"
    exemple: "Urgent: Mise à jour de votre compte"
- threat.score:
    description: "Score de menace calculé"
    type: "float"
    exemple: "0.85"
```

## 📊 Visualisations

### Vue Principale
- Type: Timeline Graph
- Objectif: Visualisation temporelle des tentatives de phishing
- Agrégations: Count by timestamp, grouped by threat level
- Filtres: threat.score > 0.6

### Vues Secondaires
1. **Top Suspicious Senders**:
   - Type: Data Table
   - Objectif: Liste des expéditeurs les plus suspects
   - Métriques: Count, Average threat score, Unique recipients

2. **Phishing Patterns**:
   - Type: Word Cloud
   - Objectif: Mots-clés communs dans les emails suspects
   - Métriques: Term frequency in suspicious emails

## ⚙️ Configuration

### Variables Kibana
```yaml
- threat_threshold:
    type: "number"
    default: "0.7"
    description: "Seuil de score de menace pour les alertes"

- time_window:
    type: "date_range"
    default: "now-24h"
    description: "Fenêtre temporelle d'analyse"
```

### Filtres Pré-configurés
```yaml
- high_threat:
    champ: "threat.score"
    opérateur: ">"
    valeur: "0.8"
```

### Intervalle de Rafraîchissement
- Par défaut: 2 minutes
- Recommandé: 1-5 minutes
- Minimum autorisé: 30 secondes

## 🎯 Seuils et Alertes

### Seuils Critiques
```yaml
- threat_score:
    warning: "> 0.7"
    critical: "> 0.9"
    description: "Score de menace phishing"

- volume:
    warning: "> 100 emails/heure"
    critical: "> 500 emails/heure"
    description: "Volume d'emails suspects"
```

## 📝 Maintenance

### Rétention des Données
- Durée de rétention: 90 jours
- Politique d'archivage: Compression après 30 jours
- Index Lifecycle Policy: o365-email-security-policy

### Performance
- Taille moyenne index: 2.5 GB/jour
- Agrégations coûteuses: Term aggregation sur email.body
- Optimisations: Utilisation de keyword fields pour les agrégations

## 🔗 Intégrations

### Dashboards Liés
- [`O365 – Email Overview – (General)`](link)
- [`O365 – Threat Intel – (Email Indicators)`](link)

### APIs/Webhooks
```yaml
- microsoft_defender:
    type: "rest"
    endpoint: "/api/defender/reports"
    usage: "Enrichissement des indicateurs de menace"
```

## 📚 Documentation

### Guides Utilisateur
- [Guide d'analyse des menaces email](link)
- [Procédures d'incident phishing](link)
- [Playbook investigation phishing](link)

### Changelog
```yaml
v1.0 (2025-06-23):
  - Version initiale
  - Intégration Microsoft Defender
  - Visualisations temps réel
```

## 🔒 Sécurité & Conformité

### Contrôle d'Accès
```yaml
roles_requis:
  - security_analyst: "Accès lecture visualisations"
  - soc_operator: "Accès complet + configuration"
```

### Conformité
- GDPR: Data minimization appliquée
- SOC2: Mapping avec contrôles de sécurité email

## 💭 Notes & Recommendations

### Bonnes Pratiques
1. Vérifier régulièrement les seuils d'alerte
2. Investiguer les pics d'activité suspecte
3. Maintenir à jour les règles de détection

### Limitations Connues
- Latence possible sur les indicateurs temps réel
- Faux positifs sur certains domaines légitimes

---

_Document v1.0 - ELK_GT Security Team_
