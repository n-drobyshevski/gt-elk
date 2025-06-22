# Monitoring - Performance et Infrastructure

## Vue d'ensemble

Ce répertoire centralise tous les aspects du monitoring de performance et de l'infrastructure de notre stack ELK.

## Structure

```plaintext
monitoring/
├── performance/               # Monitoring des performances
│   ├── dashboards/           # Dashboards de performance
│   ├── metrics-schemas/      # Schémas Metricbeat
│   └── alerts/              # Alertes de performance
├── infrastructure/           # Monitoring infrastructure
│   ├── elasticsearch/       # Monitoring ES
│   ├── logstash/           # Monitoring Logstash
│   └── kibana/             # Monitoring Kibana
└── system/                  # Monitoring système
    ├── host-metrics/        # Métriques host
    └── container-metrics/   # Métriques containers
```

## Types de Monitoring

### Performance

- Métriques de performance globales
- Monitoring applicatif
- Alertes sur les seuils critiques

### Infrastructure

- Santé des composants ELK
- Métriques des services
- Surveillance des ressources

### System

- Métriques OS et hardware
- Conteneurs Docker
- Réseau et stockage

## Conventions de Nommage

### Dashboards

- `Performance – System Overview – (Host Metrics)`
- `Performance – Elasticsearch Cluster – (Node Stats)`
- `Performance – Container Resources – (Docker)`

### Alertes

- `alert_<component>_<metric>_<threshold>.yml`
- `perf_<service>_<condition>.yml`

## Intégration avec Metricbeat

Configuration des modules :

- system
- docker  
- elasticsearch
- logstash
- kibana
