# Logstash Documentation

## Table des Matières

- [Vue d'ensemble](#vue-densemble)
- [Pipeline Principal](#pipeline-principal)
- [Configuration et Performance](#configuration-et-performance)
- [Gestion des Erreurs](#gestion-des-erreurs)
- [Monitoring](#monitoring)

## Vue d'ensemble

Logstash est utilisé dans notre architecture pour l'ingestion et le traitement des données Office 365 via l'API de gestion d'activité. Il assure la normalisation des données et leur enrichissement avant l'envoi vers Elasticsearch.

## Pipeline Principal

### Configuration du Pipeline O365

```ruby
# /etc/logstash/conf.d/o365.conf

input {
  http {
    port => 8080
    codec => json
    type => "o365_management"
  }
}

filter {
  if [type] == "o365_management" {
    # Parsing de la date
    date {
      match => [ "CreationTime", "ISO8601" ]
      target => "@timestamp"
    }

    # Enrichissement GeoIP si applicable
    if [ClientIP] {
      geoip {
        source => "ClientIP"
        target => "geoip"
      }
    }

    # Normalisation des champs
    mutate {
      rename => {
        "Id" => "event.id"
        "RecordType" => "event.category"
        "Operation" => "event.action"
        "UserType" => "user.type"
        "UserKey" => "user.id"
        "Workload" => "service.name"
      }
    }
  }
}

output {
  if [type] == "o365_management" {
    elasticsearch {
      hosts => ["es01:9200", "es02:9200"]
      index => "o365-%{+YYYY.MM.dd}"
      user => "${ELASTIC_USERNAME}"
      password => "${ELASTIC_PASSWORD}"
      ssl_enabled => true
      ssl_verification_mode => "none"
    }
  }
}
```

## Configuration et Performance

### Paramètres de Pipeline

```yaml
# logstash.yml
pipeline.workers: 2
pipeline.batch.size: 1000
pipeline.batch.delay: 50
queue.type: persisted
```

### Optimisation de la Mémoire

```yaml
# jvm.options
-Xms1g
-Xmx1g
```

### Paramètres de Performance Recommandés

1. **Pipeline Workers**
   
   - Augmenter `pipeline.workers` en fonction du nombre de cœurs CPU
   - Valeur recommandée : (nombre_de_coeurs - 1)

2. **Batch Size**
   
   - Ajuster selon le volume de données
   - Valeur par défaut : 1000 événements
   - Augmenter pour de gros volumes

3. **Queue Settings**
   
   - `queue.type: persisted` pour la durabilité
   - `queue.max_bytes: 1gb` pour limiter l'utilisation mémoire

## Gestion des Erreurs

### Dead Letter Queue

```ruby
output {
  elasticsearch {
    # Configuration standard...
    dead_letter_queue.enable: true
    dead_letter_queue.max_bytes: 1gb
  }
}
```

### Retry Policy

```ruby
output {
  elasticsearch {
    # Configuration standard...
    retry_initial_interval => 2
    retry_max_interval => 64
    retry_on_conflict => 3
  }
}
```

## Monitoring

### Metrics Configuration

```ruby
# Activation des métriques API
http.host: "0.0.0.0"
http.port: 9600

# Exemple d'accès aux métriques
# curl http://localhost:9600/_node/stats
```

### Best Practices

1. **Gestion des Ressources**
   
   - Monitorer l'utilisation CPU/RAM
   - Ajuster les batchs selon les pics d'activité
   - Utiliser des queues persistantes pour les données critiques

2. **Logging**
   
   - Activer les logs de debug en développement
   - Rotation des logs en production
   - Monitoring via Metricbeat

3. **Sécurité**
   
   - Utiliser SSL/TLS pour les communications
   - Configurer l'authentification API
   - Limiter l'accès réseau aux ports nécessaires

4. **Performance**
   
   - Optimiser les filtres Grok
   - Utiliser le multithreading efficacement
   - Implémenter le circuit breaker
