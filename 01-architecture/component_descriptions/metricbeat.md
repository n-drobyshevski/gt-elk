# Metricbeat Documentation

## Table des Matières

- [Vue d'ensemble](#vue-densemble)
- [Modules Activés](#modules-actives)
- [Configuration](#configuration)
- [Visualisations](#visualisations)
- [Alerting](#alerting)

## Vue d'ensemble

Metricbeat est déployé pour collecter les métriques de notre infrastructure Docker-ELK, permettant le monitoring des conteneurs et des services Elasticsearch.

## Modules Activés

### Module Docker

```yaml
# docker.yml
- module: docker
  metricsets:
    - "container"
    - "cpu"
    - "diskio"
    - "event"
    - "memory"
    - "network"
  hosts: ["unix:///var/run/docker.sock"]
  period: 10s
  enabled: true
```

### Module System

```yaml
# system.yml
- module: system
  period: 10s
  metricsets:
    - cpu
    - load
    - memory
    - network
    - process
    - process_summary
    - uptime
    - socket_summary
  process.include_top_n:
    by_cpu: 5
    by_memory: 5
```

### Module Elasticsearch

```yaml
# elasticsearch.yml
- module: elasticsearch
  period: 10s
  hosts: ["http://es01:9200", "http://es02:9200"]
  username: "${ELASTIC_USERNAME}"
  password: "${ELASTIC_PASSWORD}"
  xpack.enabled: true
  metricsets:
    - node
    - node_stats
    - cluster_stats
    - index
```

## Configuration

### Configuration Principale

```yaml
# metricbeat.yml
metricbeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: true
  reload.period: 10s

output.elasticsearch:
  hosts: ["es01:9200", "es02:9200"]
  username: "${ELASTIC_USERNAME}"
  password: "${ELASTIC_PASSWORD}"

setup.kibana:
  host: "kibana:5601"
  username: "${ELASTIC_USERNAME}"
  password: "${ELASTIC_PASSWORD}"

setup.dashboards.enabled: true
```

### Templating des Index

```yaml
setup.template:
  name: "metricbeat"
  pattern: "metricbeat-*"
  settings:
    index.number_of_shards: 1
    index.number_of_replicas: 1
    index.lifecycle.name: "metrics_policy"
```

## Visualisations

### Dashboards Docker

1. **Vue d'ensemble des Containers**
   - Utilisation CPU/Mémoire par container
   - I/O Disque
   - Traffic Réseau

2. **Métriques System**
   - Load Average
   - Utilisation Mémoire
   - Processus actifs

### Dashboards Elasticsearch

1. **Santé du Cluster**
   - État des nœuds
   - Allocation des shards
   - JVM Heap usage

2. **Métriques d'Index**
   - Taille des index
   - Taux d'indexation
   - Temps de réponse des requêtes

## Alerting

### Règles d'Alerte Recommandées

```yaml
# Exemple de règle d'alerte
- name: "High CPU Usage"
  type: "threshold"
  schedule: "@every 1m"
  indices: ["metricbeat-*"]
  query:
    term:
      metricset.name: cpu
  conditions:
    - when:
        gt:
          system.cpu.total.pct: 0.85
    - for: "5m"
  actions:
    - notify:
        message: "High CPU usage detected"
```

### Best Practices

1. **Collection de Métriques**
   - Adapter la période de collecte au besoin
   - Filtrer les métriques non nécessaires
   - Utiliser des tags pour identifier les services

2. **Stockage et Rétention**
   - Implémenter ILM pour les métriques
   - Archiver les anciennes données
   - Optimiser le stockage avec rollups

3. **Monitoring**
   - Surveiller l'état de Metricbeat
   - Vérifier la complétude des données
   - Monitorer les performances de collecte

4. **Alerting**
   - Définir des seuils appropriés
   - Éviter les faux positifs
   - Implémenter une escalade des alertes

5. **Maintenance**
   - Mettre à jour régulièrement les modules
   - Nettoyer les anciennes configurations
   - Documenter les changements
