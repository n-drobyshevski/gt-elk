# Elasticsearch Configuration Documentation

## Table des Matières

- [Vue d'ensemble](#vue-densemble)
- [Configuration du Cluster](#configuration-du-cluster)
- [Templates et Index](#templates-et-index)
- [Politiques ILM](#politiques-ilm)
- [Configuration des Snapshots](#configuration-des-snapshots)
- [Optimisation et Performance](#optimisation-et-performance)

## Vue d'ensemble

Elasticsearch joue un rôle central dans notre architecture en tant que moteur de stockage et de recherche distribué. Notre configuration utilise un cluster à deux nœuds pour assurer la haute disponibilité et la répartition de charge.

### Architecture du Cluster

- **Nœud 1 (ES1)** : Master + Data
- **Nœud 2 (ES2)** : Data uniquement
- **Version** : 9.0.2

## Configuration du Cluster

### Paramètres JVM et Système

```yaml
# elasticsearch.yml
cluster.name: "docker-elk"
network.host: 0.0.0.0

# Configuration des nœuds
node.name: "es01"  # ou "es02" pour le second nœud
node.master: true  # false pour ES2
node.data: true

# Paramètres JVM
-Xms2g
-Xmx2g
```

### Configuration des Shards et Réplicas

```yaml
# Index settings par défaut
index.number_of_shards: 1
index.number_of_replicas: 1

# Configuration de découverte
discovery.type: single-node  # Pour dev/test uniquement
discovery.seed_hosts: ["es01", "es02"]
cluster.initial_master_nodes: ["es01"]
```

## Templates et Index

### Template par Défaut pour les Logs O365

```json
{
  "index_patterns": ["o365-*"],
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1,
    "index.lifecycle.name": "o365_policy",
    "index.lifecycle.rollover_alias": "o365"
  },
  "mappings": {
    "dynamic": "true",
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "message": {
        "type": "text"
      }
    }
  }
}
```

## Politiques ILM

### Politique pour les Logs O365

```json
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_size": "50GB",
            "max_age": "30d"
          }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          },
          "forcemerge": {
            "max_num_segments": 1
          }
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

## Configuration des Snapshots

### Configuration du Repository

```yaml
# Snapshot repository configuration
path.repo: ["/mount/backups/es_snapshots"]

# Création du repository
PUT /_snapshot/backup_repository
{
  "type": "fs",
  "settings": {
    "location": "/mount/backups/es_snapshots"
  }
}
```

## Optimisation et Performance

### Recommandations de Tuning

1. **Memory Settings**
   
   - Heap size fixé à 2GB par nœud
   - Ne pas dépasser 50% de la RAM système
   - Disable swapping: `bootstrap.memory_lock: true`

2. **Garbage Collection**
   
   ```yaml
   -XX:+UseG1GC
   -XX:G1HeapRegionSize=32m
   -XX:+UseGCLogFileRotation
   -XX:NumberOfGCLogFiles=10
   ```

3. **File Descriptors**
   
   ```yaml
   ulimit -n 65535
   ```

### Best Practices

1. Utiliser des alias pour les index
2. Configurer des sauvegardes régulières
3. Monitorer les métriques clés via Metricbeat
4. Implémenter des politiques ILM adaptées aux besoins
