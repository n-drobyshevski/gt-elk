# Documentation Plateforme SIEM ELK_GT

## Guide de Référence Technique & d'Opérations Internes

Ce référentiel contient la documentation complète de notre plateforme de gestion des informations et événements de sécurité (SIEM) d'entreprise basée sur la pile ELK_GT (Elastic Stack 9.0.2).

## 🎯 Public Cible

- **Administrateurs SIEM** : Configuration, maintenance et procédures de déploiement de la plateforme
- **Analystes Sécurité** : Détection de menaces, réponse aux incidents et workflows de chasse aux menaces  
- **Équipes Opérations** : Surveillance infrastructure, optimisation performance et dépannage
- **Nouveaux Membres** : Documentation d'accueil et matériels de formation

## 🛡️ Vue d'Ensemble de la Plateforme

Solution de surveillance de sécurité de niveau entreprise avec architecture centrée sur les plateformes :

- **Sécurité Infrastructure** : Surveillance système et détection de menaces
- **Sécurité Office 365** : Protection de la suite de productivité cloud
- **Corrélation Multi-Plateformes** : Analyse de menaces transversale
- **Chasse Proactive aux Menaces** : Détection de menaces persistantes avancées

## 🏗️ Architecture Centrée sur les Plateformes

```plaintext
ELK_GT/
├── 01-architecture/              # 📐 Documentation Technique
│   ├── component_descriptions/   # Spécifications architecture détaillées
│   └── infrastructure_diagrams/  # Diagrammes réseau et système
├── 02-config/                   # ⚙️ Configuration Pile ELK
│   ├── elasticsearch/           # Configuration cluster ES
│   ├── logstash/               # Pipelines traitement données
│   ├── kibana/                 # Paramètres interface web
│   └── fleet/                  # Gestion agents
├── 04-security/                # 🔒 Sécurité Centrée Plateformes
│   ├── infrastructure-security/ # Sécurité système & réseau
│   │   ├── dashboards/         # Tableaux de bord surveillance infrastructure
│   │   └── detection-rules/    # Règles détection niveau système
│   ├── o365-security/          # Sécurité productivité cloud
│   │   ├── dashboards/         # Tableaux de bord surveillance Office 365
│   │   └── detection-rules/    # Règles détection spécifiques O365
│   ├── correlation-engine/     # Analyse multi-plateformes
│   │   └── multi-platform-rules/ # Corrélations transversales
│   ├── intelligence/           # Flux renseignement menaces
│   └── hunting/               # Chasse proactive menaces
├── 05-monitoring/              # 📊 Santé & Performance Plateforme
│   ├── performance/           # Métriques performance et références
│   ├── infrastructure/        # Surveillance santé infrastructure
│   └── system/               # Surveillance niveau système
└── 06-operations/             # 🔧 Opérations & Maintenance
    ├── runbooks/             # Procédures opérationnelles standard
    └── automation/           # Scripts maintenance automatisée
```

## 🔧 Pile Technique

### Composants Principaux

- **Elasticsearch 9.0.2** : Moteur de recherche et d'analytique distribué (cluster 2 nœuds)
- **Logstash** : Pipeline traitement données avec configurations spécialisées O365
- **Kibana** : Interface web avec capacités de gestion Fleet
- **Fleet Server** : Gestion centralisée et déploiement d'agents

### Prérequis Infrastructure

```yaml
Minimum par nœud :
  CPU: 4 cœurs (8+ recommandé)
  RAM: 16 GB (32+ recommandé production)
  Stockage: 100 GB SSD (500+ GB production)
  Réseau: 1 Gbps
Docker: 20.10+
```

### Intégrations

- **Office 365** : 9 intégrations spécialisées (Exchange, SharePoint, Teams, etc.)
- **Surveillance Infrastructure** : Métriques système, logs et APM
- **Renseignement Menaces** : Flux de menaces externes et internes
- **Applications Personnalisées** : Ingestion logs basée API

## 🚀 Navigation Rapide

### Accès aux Interfaces

- **Interface Kibana** : `http://localhost:5601` (admin/changeme)
- **Gestion Fleet** : `http://localhost:5601/app/fleet`
- **Surveillance Cluster** : `curl localhost:9200/_cluster/health`

### Configuration par Plateforme

#### Sécurité Infrastructure

- Configuration : [`04-security/infrastructure-security/README.md`](04-security/infrastructure-security/README.md)
- Import Tableaux de Bord : [`04-security/infrastructure-security/dashboards/`](04-security/infrastructure-security/dashboards/)
- Règles de Détection : [`04-security/infrastructure-security/detection-rules/`](04-security/infrastructure-security/detection-rules/)

#### Sécurité Office 365

- Configuration Intégration : [`04-security/o365-security/README.md`](04-security/o365-security/README.md)
- Configuration API : [`02-config/logstash/o365-pipelines.conf`](02-config/logstash/o365-pipelines.conf)
- Configuration Tableaux de Bord : [`04-security/o365-security/dashboards/`](04-security/o365-security/dashboards/)

## 📚 Documentation & Procédures

### Documentation Essentielle

| Document | Objectif | Public |
|----------|----------|--------|
| [Vue d'ensemble Architecture](01-architecture/README.md) | Conception système et composants | Administrateurs, Architectes |
| [Guide Configuration](02-config/CONFIG.md) | Procédures déploiement et configuration | Administrateurs |
| [Framework Sécurité](04-security/SECURITY.md) | Politiques et procédures sécurité | Équipe Sécurité |
| [Structure Centrée Plateformes](04-security/PLATFORM_CENTRIC_DETECTION_RULES.md) | Organisation règles détection | Analystes Sécurité |
| [Runbooks Opérationnels](06-operations/runbooks/README.md) | Procédures opérationnelles standard | Équipe Opérations |

### Normes & Conventions

- [Convention Nommage Tableaux de Bord](06-reference/standards/CONVENTION_NOMMAGE_DASHBOARDS.md)
- [Standard Tags Kibana](06-reference/standards/CONVENTION_TAGS_DASHBOARDS_KIBANA.md)  
- [Modèle Règle Observabilité](06-reference/standards/TEMPLATE_SIGMA_OBSERVABILITE.yaml)
- [Modèle Documentation Tableau de Bord](06-reference/standards/TEMPLATE_DOCUMENTATION_DASHBOARD.md)

## 🔍 Cas d'Usage Sécurité

### Protection Infrastructure

- Surveillance connexions système et détection d'anomalies
- Analyse trafic réseau et identification menaces
- Contrôle accès ressources et détection élévation privilèges
- Indicateurs malware et ransomware

### Protection Office 365

- Sécurité email et détection phishing
- Surveillance accès SharePoint et OneDrive
- Sécurité collaboration Teams
- Gestion identité et accès

### Analyse Transversale

- Détection attaques multi-vecteurs
- Chasse menaces persistantes avancées (APT)
- Identification menaces internes
- Surveillance conformité et rapports


### Standards Documentation

- Suivre les [Standards Documentation](06-reference/standards/documentation-standards.md)
- Utiliser principes organisation centrée plateformes
- Inclure exemples pratiques et runbooks
- Maintenir précision technique et focus opérationnel

### Processus de Modification

1. **Processus Révision** : Toutes modifications révisées par experts métier
2. **Exigences Tests** : Tester procédures en environnement développement
3. **Workflow Approbation** : Révision technique → Révision sécurité → Approbation
4. **Documentation** : Mettre à jour runbooks et procédures pertinents

### Directives Qualité

- **Clarté** : Écrire pour professionnels techniques niveaux expérience variés
- **Complétude** : Inclure dépannage, exemples et cas limites
- **Maintenance** : Révisions régulières et mises à jour précision
- **Accessibilité** : Documentation facilement découvrable et cherchable

## 📈 Évolution Plateforme & Feuille de Route

### Statut Actuel

- **Maturité Plateforme** : Prêt production avec objectif disponibilité 99.9%
- **Couverture Sécurité** : Infrastructure et O365 entièrement intégrés
- **Préparation Équipe** : 12 analystes formés, 4 administrateurs plateforme

### Améliorations à Venir



### Vision Long Terme

- Visibilité sécurité entreprise complète
- Capacités réponse menaces automatisées  
- Analytique sécurité prédictive
- Rapports conformité sans intervention

---

## 📋 Liens de Référence Rapide

### Usage Quotidien

- [Tableau de Bord Kibana](http://localhost:5601) | [Gestion Fleet](http://localhost:5601/app/fleet)
- [Tableaux de Bord Sécurité](04-security/) | [Surveillance Performance](05-monitoring/)
- [Runbook Quotidien](06-operations/runbooks/daily-operations.md)

### Documentation

- [Index Documentation Complète](docs/README.md)
- [Référence API](docs/api-reference.md)  
- [Guide Dépannage](docs/troubleshooting.md)
