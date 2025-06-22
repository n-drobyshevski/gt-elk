# Configuration ELK Stack

## Vue d'ensemble

Ce répertoire contient les fichiers de configuration essentiels pour notre déploiement ELK (Elasticsearch, Logstash, Kibana) basé sur Docker, avec les intégrations Office 365 et notre cluster Elasticsearch à deux nœuds.

## Structure du Répertoire

```plaintext
config/
├── elasticsearch/         # Configuration du cluster ES
│   ├── elasticsearch.yml  # Configuration principale
│   ├── jvm.options       # Paramètres JVM
│   └── users/            # Configuration RBAC
├── logstash/             # Configuration Logstash
│   ├── pipelines.yml     # Définition des pipelines
│   └── o365/            # Configuration O365
├── kibana/              # Configuration Kibana
│   └── kibana.yml       # Paramètres principaux
└── fleet/               # Configuration Fleet
    └── fleet.yml        # Paramètres Fleet Server
```

## Gestion des Configurations

### Points Clés

- Utilisation des variables d'environnement dans Docker Compose
- Stockage sécurisé des secrets (hors Git)
- Validation des configurations avant déploiement
- Suivi des modifications via Git

### Sécurité

- Activation des fonctionnalités X-Pack
- TLS pour les communications inter-nœuds
- RBAC (Role-Based Access Control)
- Rotation trimestrielle des mots de passe
- Crédientials dédiés pour l'API O365

## Gestion Fleet

La configuration du Fleet Server est intégrée avec Elasticsearch pour :

- Gestion des politiques d'agents
- Monitoring centralisé
- Déploiement automatisé des mises à jour
- Reporting de santé

## Documentation Externe

- [Configuration Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
- [Configuration Logstash](https://www.elastic.co/guide/en/logstash/current/configuration.html)
- [Configuration Kibana](https://www.elastic.co/guide/en/kibana/current/settings.html)
- [Paramètres Fleet Server](https://www.elastic.co/guide/en/fleet/current/fleet-server.html)

## Best Practices

1. **Gestion des changements**
   
   - Tester les modifications en développement
   - Valider la syntaxe des fichiers YAML
   - Documenter les changements

2. **Sécurité**
   
   - Ne jamais commiter de secrets
   - Utiliser des variables d'environnement
   - Restreindre les accès aux fichiers

3. **Maintenance**
   
   - Sauvegarder les configurations
   - Vérifier régulièrement les logs
   - Mettre à jour la documentation
