# Sécurité - Structure Centralisée

## Vue d'ensemble

Ce répertoire centralise tous les aspects de la sécurité de notre infrastructure ELK et de l'analyse des données Office 365.

## Structure

```plaintext
security/
├── o365-security/              # Sécurité Office 365
│   ├── dashboards/            # Dashboards O365 sécurisés
│   ├── detection-rules/       # Règles spécifiques O365
│   ├── hunting-queries/       # Requêtes de chasse aux menaces O365
│   └── integrations/         # Configurations Fleet O365
├── intelligence/              # Threat Intelligence
│   ├── iocs/                 # Indicateurs de compromission
│   ├── threat-feeds/         # Flux de menaces
│   └── attribution/          # Attribution des menaces
├── detection/                 # Règles de détection
│   ├── sigma-rules/          # Règles Sigma
│   ├── custom-rules/         # Règles personnalisées
│   └── correlation-rules/    # Règles de corrélation
├── hunting/                   # Chasse aux menaces
│   ├── playbooks/            # Playbooks de chasse
│   ├── queries/              # Requêtes de recherche
│   └── procedures/           # Procédures d'investigation
└── infrastructure-security/   # Sécurité infrastructure ELK
    ├── dashboards/           # Dashboards sécurité ELK
    ├── access-control/       # Contrôle d'accès
    └── compliance/           # Conformité
```

## Organisation par Domaine

### Office 365 Security
Focus sur la sécurité des services Office 365 :
- Authentification et MFA
- Détection de phishing
- Protection des données (DLP)
- Analyse comportementale

### Threat Intelligence
Centralisation de l'intelligence des menaces :
- Sources ouvertes et commerciales
- IOCs automatisés
- Attribution et TTPs

### Detection Engineering
Développement et maintenance des règles :
- Règles SIGMA alignées MITRE ATT&CK
- Corrélation multi-sources
- Faux positifs et tuning

### Threat Hunting
Chasse proactive aux menaces :
- Hypothèses basées sur la TI
- Requêtes avancées
- Playbooks structurés

## Conventions de Nommage

### Dashboards Sécurité
- `Security – O365 Audit Overview`
- `Security – Failed Authentication – (By Location)`
- `Security – Threat Detection – (Last 24h)`

### Règles de Détection
- `sigma_rule_<technique_id>_<description>.yml`
- `custom_<source>_<attack_pattern>.yml`

### Playbooks
- `hunting_<threat_type>_<scenario>.md`
- `investigation_<incident_type>.md`

## Intégration

Cette structure s'intègre avec :
- Fleet Server pour la gestion des agents
- Elasticsearch pour le stockage
- Kibana pour la visualisation
- APIs externes pour la threat intelligence
