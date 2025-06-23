# Kibana - Interface Web Sécurisée

## Table des Matières

- [Vue d'ensemble](#vue-densemble)
- [Configuration TLS](#configuration-tls)
- [Configuration](#configuration)
- [Gestion des Spaces](#gestion-des-spaces)
- [Dashboards O365](#dashboards-o365)
- [Fleet et Intégrations](#fleet-et-integrations)
- [Sécurité et Accès](#securite-et-acces)

## Vue d'ensemble

Kibana est l'interface de visualisation et de gestion pour notre stack Elastic. Il permet la création de dashboards, la gestion des intégrations via Fleet, et l'administration du cluster Elasticsearch.

## Configuration

### Configuration de Base

```yaml
# kibana.yml
server.name: kibana
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://es01:9200", "http://es02:9200"]

# Sécurité
elasticsearch.username: "${ELASTIC_USERNAME}"
elasticsearch.password: "${ELASTIC_PASSWORD}"
xpack.security.enabled: true

# Configuration des sessions
xpack.security.session.idleTimeout: "1h"
xpack.security.session.lifespan: "24h"
```

## Configuration TLS

### Accès Sécurisé

- **Port** : 5601 avec TLS
- **URL** : `https://localhost:5601`
- **Certificats** : Utilisation des certificats partagés du volume `certs`

### Authentification

- **Connexion Elasticsearch** : Utilisateur `kibana_system` avec mot de passe sécurisé
- **Interface utilisateur** : Authentification via utilisateurs Elasticsearch
- **Accès par défaut** : `elastic` / `changeme` (à modifier après installation)

### Configuration

```yaml
elasticsearch.hosts: ["https://elasticsearch:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "${KIBANA_SYSTEM_PASSWORD}"
elasticsearch.ssl.certificateAuthorities: ["/usr/share/kibana/config/certs/ca/ca.crt"]
server.ssl.enabled: true
server.ssl.certificate: "/usr/share/kibana/config/certs/kibana/kibana.crt"
server.ssl.key: "/usr/share/kibana/config/certs/kibana/kibana.key"
```

## Gestion des Spaces

### Structure des Spaces

1. **Production**
   
   - Dashboards en lecture seule
   - Visualisations optimisées
   - Accès restreint

2. **Développement**
   
   - Environnement de test
   - Création de dashboards
   - Tests d'intégrations

### Export/Import des Assets

```bash
# Export d'un dashboard
POST /api/saved_objects/_export
{
  "type": ["dashboard", "visualization", "index-pattern"],
  "objects": [{
    "type": "dashboard",
    "id": "o365-audit-overview"
  }]
}

# Import via fichier .ndjson
POST /api/saved_objects/_import
```

## Dashboards O365

### Structure des Dashboards

1. **Vue d'ensemble O365**
   
   ```json
   {
     "attributes": {
       "title": "O365 - Vue Générale",
       "hits": 0,
       "description": "Dashboard principal O365",
       "panelsJSON": "[...]"
     }
   }
   ```

2. **Activité Exchange**
   
   ```json
   {
     "attributes": {
       "title": "O365 - Exchange Activity",
       "hits": 0,
       "description": "Activité Exchange Online",
       "panelsJSON": "[...]"
     }
   }
   ```

## Fleet et Intégrations

### Configuration O365

```yaml
# Configuration de l'intégration O365
name: "O365 Integration"
id: o365
version: 1.0.0
package:
  name: o365
policy_templates:
  - name: o365
    title: Microsoft Office 365 logs
    description: Collect Microsoft Office 365 logs
    inputs:
      - type: o365
        vars:
          - name: tenant_id
            type: text
          - name: client_id
            type: text
          - name: client_secret
            type: password
```

### Gestion des Policies

1. **Création de Policy**
   
   - Sélection des intégrations
   - Configuration des credentials
   - Assignation aux agents

2. **Monitoring**
   
   - État des agents
   - Statut des intégrations
   - Logs d'erreurs

## Sécurité et Accès

### Rôles et Permissions

```yaml
# Exemple de rôle
role_name: "o365_viewer"
elasticsearch:
  cluster: []
  indices:
    - names: ["o365-*"]
      privileges: ["read"]
kibana:
  feature:
    dashboard: ["read"]
    discover: ["read"]
```

### Best Practices

1. **Gestion des Dashboards**
   
   - Utiliser des variables globales
   - Optimiser les requêtes
   - Documenter les visualisations

2. **Performance**
   
   - Limiter le nombre de visualisations par dashboard
   - Utiliser des timeranges appropriés
   - Implémenter le caching quand possible

3. **Maintenance**
   
   - Sauvegarder régulièrement les objets
   - Nettoyer les visualisations non utilisées
   - Maintenir une documentation à jour

4. **Sécurité**
   
   - Implémenter l'authentification SSO
   - Utiliser des rôles granulaires
   - Auditer les accès régulièrement
