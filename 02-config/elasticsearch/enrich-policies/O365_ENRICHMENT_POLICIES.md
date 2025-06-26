# Politiques d'Enrichissement O365

## Vue d'ensemble

Ce document décrit la configuration des politiques d'enrichissement Elasticsearch pour la validation géographique des connexions Office 365 basée sur des listes blanches CSV.

## Architecture des Données

### Sources CSV

#### 1. Fichier `user_allowed.csv`
```csv
tenant_user,allowed
contoso|alice@contoso.com,US|GB|FR
contoso|bob@contoso.com,US|DE
fabrikam|john@fabrikam.com,GB|FR|ES
```

#### 2. Fichier `tenant_allowed.csv`
```csv
tenant,allowed
contoso,US|GB|FR|DE|ES
fabrikam,GB|FR|ES|IT
```

### Index Elasticsearch

#### Index `user_allowed_countries`
- **Document ID** : `tenant_user` (ex: "contoso|alice@contoso.com")
- **Champs** :
  - `tenant_user` : Clé composite tenant|utilisateur
  - `tenant.id` : ID du tenant (ex: "contoso")
  - `user.name` : Email utilisateur (ex: "alice@contoso.com")
  - `allowed` : Tableau des codes pays autorisés ["US", "GB", "FR"]

#### Index `tenant_allowed_countries`
- **Document ID** : `tenant` (ex: "contoso")
- **Champs** :
  - `tenant.id` : ID du tenant (ex: "contoso")
  - `allowed` : Tableau des codes pays autorisés ["US", "GB", "FR", "DE", "ES"]
  - `countries_count` : Nombre de pays autorisés

## Configuration des Politiques d'Enrichissement

### 1. Politique Tenant Countries

```bash
curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/tenant-countries" \
  -H "Content-Type: application/json" \
  -u elastic:password \
  -d '{
    "match": {
      "indices": "tenant_allowed_countries",
      "match_field": "tenant.id",
      "enrich_fields": ["allowed", "countries_count"]
    }
  }'
```

### 2. Politique User-Tenant Countries

```bash
curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/user-tenant-countries" \
  -H "Content-Type: application/json" \
  -u elastic:password \
  -d '{
    "match": {
      "indices": "user_allowed_countries",
      "match_field": "tenant_user",
      "enrich_fields": ["allowed", "tenant.id", "user.name"]
    }
  }'
```

### 3. Exécution des Politiques

```bash
# Exécution de la politique tenant
curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/tenant-countries/_execute" \
  -u elastic:password

# Exécution de la politique user-tenant
curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/user-tenant-countries/_execute" \
  -u elastic:password
```

## Pipeline d'Ingestion avec Enrichissement

### Configuration du Pipeline `o365-enrich-pipeline`

```json
PUT /_ingest/pipeline/o365-enrich-pipeline
{
  "description": "Enrichissement O365 avec validation géographique",
  "processors": [
    {
      "enrich": {
        "policy_name": "tenant-countries",
        "field": "organization.id",
        "target_field": "tenant_geo",
        "max_matches": 1,
        "on_failure": [
          {
            "set": {
              "field": "tenant_geo.allowed",
              "value": []
            }
          }
        ]
      }
    },
    {
      "script": {
        "description": "Construction du champ tenant_user",
        "source": """
          if (ctx.organization?.id != null && ctx.user?.email != null) {
            ctx.tenant_user = ctx.organization.id + '|' + ctx.user.email;
          }
        """
      }
    },
    {
      "enrich": {
        "policy_name": "user-tenant-countries",
        "field": "tenant_user",
        "target_field": "user_geo",
        "max_matches": 1,
        "on_failure": [
          {
            "set": {
              "field": "user_geo.allowed",
              "value": []
            }
          }
        ]
      }
    },
    {
      "script": {
        "description": "Validation géographique et marquage",
        "source": """
          String sourceCountry = ctx.source?.geo?.country_iso_code;
          if (sourceCountry == null) return;
          
          // Vérification au niveau utilisateur (priorité)
          if (ctx.user_geo?.allowed != null && 
              ctx.user_geo.allowed instanceof List && 
              ctx.user_geo.allowed.size() > 0) {
            ctx.user_whitelisted = ctx.user_geo.allowed.contains(sourceCountry);
          } 
          // Fallback sur validation tenant
          else if (ctx.tenant_geo?.allowed != null && 
                   ctx.tenant_geo.allowed instanceof List) {
            ctx.user_whitelisted = ctx.tenant_geo.allowed.contains(sourceCountry);
          } 
          else {
            ctx.user_whitelisted = false;
          }
          
          // Validation tenant globale
          if (ctx.tenant_geo?.allowed != null && 
              ctx.tenant_geo.allowed instanceof List) {
            ctx.tenant_whitelisted = ctx.tenant_geo.allowed.contains(sourceCountry);
          } else {
            ctx.tenant_whitelisted = false;
          }
        """
      }
    }
  ]
}
```

## Application du Pipeline aux Data Streams

### Configuration Fleet/Elastic Agent

```yaml
# Dans la configuration de l'intégration O365
output:
  default:
    type: elasticsearch
    hosts: ["https://elasticsearch:9200"]
    pipeline: "o365-enrich-pipeline"
```

### Configuration Index Template

```json
PUT /_index_template/logs-o365-enriched
{
  "index_patterns": ["logs-o365.audit-*"],
  "data_stream": {},
  "template": {
    "settings": {
      "index.default_pipeline": "o365-enrich-pipeline"
    },
    "mappings": {
      "properties": {
        "user_whitelisted": { "type": "boolean" },
        "tenant_whitelisted": { "type": "boolean" },
        "tenant_user": { "type": "keyword" },
        "tenant_geo": {
          "properties": {
            "allowed": { "type": "keyword" },
            "countries_count": { "type": "integer" }
          }
        },
        "user_geo": {
          "properties": {
            "allowed": { "type": "keyword" },
            "tenant.id": { "type": "keyword" },
            "user.name": { "type": "keyword" }
          }
        }
      }
    }
  }
}
```

## Utilisation dans Kibana

### Requêtes KQL Utiles

```kql
# Connexions depuis des pays non autorisés
user_whitelisted:false AND event.action:"UserLoggedIn"

# Connexions tenant non autorisées
tenant_whitelisted:false

# Utilisateurs avec règles spécifiques
user_geo.allowed:* AND NOT tenant_geo.allowed:*

# Statistiques par pays autorisé
tenant_geo.allowed:*
```

### Alertes Recommandées

1. **Connexion pays non autorisé (utilisateur)**
   - Condition : `user_whitelisted:false`
   - Seuil : 1 occurrence
   - Action : Alerte immédiate

2. **Connexion pays non autorisé (tenant)**
   - Condition : `tenant_whitelisted:false`
   - Seuil : 1 occurrence
   - Action : Notification SOC

## Maintenance

### Mise à jour des Listes Blanches

1. **Mise à jour fichier CSV**
   ```bash
   # Copier le nouveau fichier
   cp new_user_allowed.csv /var/data/csv/user_allowed.csv
   
   # Logstash détectera automatiquement les changements
   ```

2. **Re-exécution des politiques**
   ```bash
   # Après mise à jour CSV, re-exécuter les politiques
   curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/tenant-countries/_execute"
   curl -X PUT "https://<ELASTIC_HOST>:9200/_enrich/policy/user-tenant-countries/_execute"
   ```

### Monitoring

- **Index Health** : Surveiller la taille des index `*_allowed_countries`
- **Pipeline Performance** : Monitorer les métriques du pipeline `o365-enrich-pipeline`
- **Enrichment Failures** : Alertes sur les échecs d'enrichissement

---

**Dernière mise à jour** : 2025-06-26  
**Responsable** : Équipe Sécurité IT
