# ELK_GT - Stack ELK Sécurité Avancée

## 🛡️ Vue d'Ensemble

Solution avancée de monitoring sécurité basée sur Elastic Stack (9.0.2), orientée plateforme avec focus particulier sur :

- La sécurité de l'infrastructure
- La sécurité Office 365
- La corrélation multi-plateformes
- Le threat hunting proactif

## 🏗️ Architecture Platform-Centric

```plaintext
ELK_GT/
├── 01-architecture/            # Documentation technique
│   ├── component_descriptions/ # Architecture détaillée
│   └── infrastructure_diagrams/# Diagrammes techniques
├── 02-config/                 # Configuration ELK
│   ├── elasticsearch/         # Configuration ES
│   ├── logstash/             # Pipelines et filtres
│   ├── kibana/               # Configuration Kibana
│   └── fleet/                # Gestion des agents
├── 04-security/              # 🔒 Sécurité (platform-centric)
│   ├── infrastructure-security/# Sécurité infrastructure
│   │   ├── dashboards/       # Visualisation sécurité
│   │   └── detection-rules/  # Règles de détection
│   ├── o365-security/        # Sécurité Office 365
│   │   ├── dashboards/       # Tableaux de bord O365
│   │   └── detection-rules/  # Détection O365
│   ├── correlation-engine/    # Corrélation avancée
│   │   └── multi-platform-rules/ # Règles cross-platform
│   ├── intelligence/         # Threat Intelligence
│   └── hunting/             # Threat Hunting
├── 05-monitoring/            # 📊 Monitoring technique
│   ├── performance/         # Métriques performance
│   ├── infrastructure/      # Santé infrastructure
│   └── system/             # Monitoring système
└── 06-operations/           # 🔧 Opérations
    ├── runbooks/           # Procédures
    └── automation/         # Scripts
```

## 🎯 Points Forts

### 1. Architecture Platform-Centric

- Organisation par plateforme (infrastructure, O365)
- Pas de dashboards génériques
- Spécialisation des équipes
- Structure extensible

### 2. Stack Technique

- **Elasticsearch** : Cluster 2 nœuds (9.0.2)
- **Logstash** : Pipelines O365 optimisés
- **Kibana** : Interface et Fleet
- **Fleet Server** : Gestion centralisée

### 3. Intégrations

- Office 365 (9 intégrations)
- Infrastructure (Métriques système)
- Threat Intelligence
- APM et logs

## 🚀 Démarrage Rapide

### 1. Prérequis

```bash
# Ressources minimales par nœud
CPU: 4 cores
RAM: 16 GB
Disk: 100 GB SSD
Docker: 20.10+
```

### 2. Installation

```bash
# Cloner le repo
git clone https://github.com/org/ELK_GT
cd ELK_GT

# Démarrer avec Docker Compose
docker-compose up -d
```

### 3. Configuration Initiale

1. **Infrastructure** : [`04-security/infrastructure-security/README.md`](04-security/infrastructure-security/README.md)
2. **Office 365** : [`04-security/o365-security/README.md`](04-security/o365-security/README.md)
3. **Corrélation** : [`04-security/correlation-engine/README.md`](04-security/correlation-engine/README.md)

## 📚 Documentation

### Guides Essentiels

- [Architecture Détaillée](01-architecture/README.md)
- [Guide de Configuration](02-config/CONFIG.md)
- [Framework Sécurité](04-security/SECURITY.md)
- [Structure Platform-Centric](04-security/PLATFORM_CENTRIC_DETECTION_RULES.md)

### Documentation Technique

- [Elastic Stack](01-architecture/component_descriptions/README.md)
- [Pipelines Logstash](02-config/logstash/README.md)
- [Intégration O365](04-security/o365-security/README.md)
- [Dashboards](06-reference/standards/CONVENTION_NOMMAGE_DASHBOARDS.md)

## 🛠️ Maintenance

### Monitoring

- [Performance](05-monitoring/performance/README.md)
- [Infrastructure](05-monitoring/infrastructure/README.md)
- [Système](05-monitoring/system/README.md)

### Opérations

- [Runbooks](06-operations/runbooks/README.md)
- [Automatisation](06-operations/automation/README.md)
- [Backup](06-operations/backup-procedures/README.md)

## 🤝 Contribution

1. **Standards**
   
   - Suivre les conventions de nommage
   - Respecter l'approche platform-centric
   - Documenter les changements

2. **Processus**
   
   - Tester en environnement de dev
   - Valider les dashboards
   - Vérifier les règles de détection

3. **Documentation**
   
   - Maintenir les READMEs à jour
   - Documenter les cas d'usage
   - Partager les bonnes pratiques

## 📋 Standards & Conventions

- [Convention de Nommage Dashboards](06-reference/standards/CONVENTION_NOMMAGE_DASHBOARDS.md)
- [Tags Kibana](06-reference/standards/CONVENTION_TAGS_DASHBOARDS_KIBANA.md)
- [Template Observabilité](06-reference/standards/TEMPLATE_SIGMA_OBSERVABILITE.yaml)

## 📈 Évolution

Consultez notre [PLATFORM_CENTRIC_TRANSFORMATION_COMPLETE.md](PLATFORM_CENTRIC_TRANSFORMATION_COMPLETE.md) pour comprendre :

- La transformation platform-centric
- Les bénéfices réalisés
- Les prochaines étapes

## 📞 Support

- **Documentation** : Voir [`/docs`](docs/)
- **Issues** : Utiliser le bug tracker
- **Questions** : Contacter l'équipe sécurité
