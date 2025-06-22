# Performance Monitoring Dashboards

Ce répertoire contient les tableaux de bord centralisés pour la surveillance des performances.

## Structure

### `/system`

- Métriques au niveau du système d'exploitation
- Utilisation des ressources (CPU, mémoire, disque, réseau)
- Métriques kernel et système
- Performance des hôtes

### `/applications`

- Performance des applications
- Métriques Elasticsearch
- Métriques Logstash
- Performance Office 365

### `/infrastructure`

- Métriques des conteneurs Docker
- Performance du cluster
- Métriques réseau
- Surveillance du stockage

## Conventions de Nommage

Format : `{Domain} – {Function/Metric} – (Optional Context)`

Exemples :

- `Performance – System Overview – (Host Metrics)`
- `Performance – Elasticsearch Cluster – (Node Stats)`
- `Performance – Container Resources – (Docker)`
- `Performance – Network Usage – (Per Interface)`

## Intégration avec Metricbeat

Ces dashboards utilisent principalement les données collectées par Metricbeat avec les modules suivants :

- system
- docker
- elasticsearch
- logstash
- network

## Best Practices

### Configuration des Dashboards

- Timerange par défaut : dernières 24h
- Refresh rate : 1 minute pour les métriques système
- Agrégations optimisées pour les performances
- Utilisation de variables Kibana pour le filtrage

### Visualisations Recommandées

- Graphiques de tendance pour l'utilisation des ressources
- Jauges pour les métriques instantanées
- Heat maps pour les patterns de charge
- Tables pour les tops N consommateurs

### Alerting

Pour la configuration des alertes basées sur ces métriques, voir le répertoire `detection-rules/`

## Maintenance

### Rétention des Données

- Métriques système : 30 jours
- Métriques application : 90 jours
- Métriques infrastructure : 60 jours

### Optimisation

- Revue mensuelle des patterns d'index
- Optimisation des agrégations
- Ajustement des intervalles de collecte

## Documentation

Chaque dashboard doit inclure :

- Description de l'objectif
- Sources de données et index patterns
- Variables et filtres disponibles
- Seuils d'alerte recommandés
