# Fleet - Gestion centralisée des agents Elastic

## Sommaire

- [Vue d'ensemble](#vue-densemble)
- [Architecture Fleet Server](#architecture-fleet-server)
- [Gestion des politiques](#gestion-des-politiques)
- [Sécurité et inscription](#sécurité-et-inscription)
- [Bonnes pratiques](#bonnes-pratiques)

## Vue d'ensemble

Fleet est la solution centralisée de gestion des agents Elastic dans notre infrastructure ELK. Il permet de :

- Gérer le déploiement et la configuration des agents Elastic
- Administrer les politiques d'intégration (dont Office 365)
- Surveiller l'état des agents
- Mettre à jour les configurations de manière centralisée

### Configuration actuelle

Notre déploiement Fleet est intégré à notre cluster Elasticsearch à deux nœuds et gère principalement :

- Les agents Elastic pour l'intégration Office 365
- Metricbeat pour la surveillance du cluster
- Les agents système sur les différents hôtes

## Architecture Fleet Server

### Composants clés

```yaml
fleet:
  server:
    host: "0.0.0.0"
    port: 8220
    ssl:
      enabled: true
      certificate: "/etc/fleet/certs/fleet-server.crt"
      key: "/etc/fleet/certs/fleet-server.key"
  elasticsearch:
    hosts: 
      - "https://es01:9200"
      - "https://es02:9200"
    ssl:
      certificate_authorities: ["/etc/fleet/certs/ca.crt"]
```

### Communication avec les agents

Le Fleet Server communique avec les agents via HTTPS (port 8220) et utilise l'authentification par certificats pour sécuriser les échanges.

## Gestion des politiques

### Politique Office 365

```yaml
policy:
  id: "office365-monitoring"
  name: "Office 365 Monitoring"
  namespace: "default"
  monitoring:
    enabled: true
    use_output: "default"
    logs: true
    metrics: true
  inputs:
    - type: "o365audit"
      vars:
        tenants:
          - id: "tenant-id"
            name: "Primary Tenant"
        content_types:
          - Audit.AzureActiveDirectory
          - Audit.Exchange
          - Audit.SharePoint
          - Audit.General
```

### Configuration des outputs

```yaml
outputs:
  default:
    type: "elasticsearch"
    hosts: 
      - "https://es01:9200"
      - "https://es02:9200"
    load_balance: true
    ssl:
      certificate_authorities: ["/etc/fleet/certs/ca.crt"]
```

## Sécurité et inscription

### Processus d'inscription des agents

1. Génération du token d'inscription dans Kibana
2. Configuration de l'agent avec le token
3. Validation de l'identité via TLS mutuel
4. Attribution des politiques

### Gestion des certificats

```yaml
ssl:
  verification_mode: "full"
  supported_protocols: ["TLSv1.2", "TLSv1.3"]
  cipher_suites:
    - "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384"
    - "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384"
```

## Bonnes pratiques

### Surveillance et performance

- Monitoring des métriques Fleet Server via Metricbeat
- Alertes sur l'état des agents
- Surveillance des temps de réponse du serveur

### Configuration recommandée

```yaml
fleet.agent.monitoring:
  enabled: true
  collection_interval: 30s
  use_output: "default"

fleet.server:
  timeout: 30s
  max_connections: 1000
  connection_timeout: 30s
```

### Sauvegarde et maintenance

- Sauvegarde régulière des politiques via l'API Fleet
- Rotation des certificats tous les 6 mois
- Mise à jour planifiée des agents

### Optimisation

- Load balancing entre les nœuds Elasticsearch
- Mise en cache des politiques
- Compression des communications agent-serveur
