# Configuration Technique Logstash - O365 Geo-Enrichissement

## Vue d'Ensemble

Cette documentation décrit la configuration multi-pipeline Logstash pour l'enrichissement géographique des données Office 365, incluant la gestion des listes blanches CSV et l'intégration avec les politiques d'enrichissement Elasticsearch.

## Architecture des Pipelines

### 1. Pipeline Principal O365 (`o365-main`)

**Objectif** : Traitement en temps réel des événements O365 avec enrichissement géographique.

**Configuration** :
- **Workers** : 4 (optimisé pour le débit)
- **Batch Size** : 500 événements
- **Batch Delay** : 50ms
- **Auto-reload** : Activé (10s)

**Flux de données** :
```
Elastic Agent → Beats Input (TLS) → GeoIP Enrichment → Elasticsearch (avec pipeline)
```

**Champs ajoutés** :
- `tenant_user_lookup` : `{OrganizationId}|{UserId}`
- `tenant_lookup` : `{OrganizationId}`
- `client.geo.*` : Géolocalisation IP
- `@metadata.enrichment_pipeline` : Référence pipeline

### 2. Pipeline Listes Blanches Utilisateurs (`user-allowed`)

**Objectif** : Import des listes blanches par combinaison tenant-utilisateur.

**Configuration** :
- **Workers** : 1 (séquentiel pour CSV)
- **Batch Size** : 100 enregistrements
- **Batch Delay** : 100ms
- **Auto-reload** : Activé (30s)

**Format CSV attendu** :
```csv
tenant_user,allowed
contoso|alice@contoso.com,US|GB|DE
fabrikam|bob@fabrikam.com,FR|BE
```

**Index de destination** : `user_allowed_countries`
**Document ID** : `{tenant}|{user}` (clé composite)

### 3. Pipeline Listes Blanches Tenants (`tenant-allowed`)

**Objectif** : Import des listes blanches par tenant.

**Configuration** :
- **Workers** : 1 (séquentiel pour CSV)
- **Batch Size** : 100 enregistrements
- **Batch Delay** : 100ms
- **Auto-reload** : Activé (30s)

**Format CSV attendu** :
```csv
tenant,allowed
contoso,US|GB|DE|FR
fabrikam,FR|BE|LU
```

**Index de destination** : `tenant_allowed_countries`
**Document ID** : `{tenant}`

## Sécurité TLS

### Configuration SSL/TLS

Tous les pipelines utilisent une connexion TLS sécurisée vers Elasticsearch :

```yaml
ssl: true
cacert: "/etc/logstash/certs/ca.crt"
user: "${ES_USER}"
password: "${ES_PASS}"
```

### Certificats Requis

| Fichier | Usage | Description |
|---------|--------|-------------|
| `/etc/logstash/certs/ca.crt` | Autorité de certification | Validation des certificats Elasticsearch |
| `/etc/logstash/certs/logstash.crt` | Certificat Logstash | Authentification du service |
| `/etc/logstash/certs/logstash.key` | Clé privée Logstash | Chiffrement des communications |

## Variables d'Environnement

### Variables Requises

```bash
# Authentification Elasticsearch
export ES_USER="logstash_writer"
export ES_PASS="secure_password_here"

# Configuration Logstash
export LS_JAVA_OPTS="-Xmx4g -Xms4g"
export LOGSTASH_KEYSTORE_PASS="keystore_password"
```

## Surveillance et Monitoring

### Métriques Clés

1. **Pipeline Principal O365** :
   - Débit d'événements/seconde
   - Latence d'enrichissement
   - Taux d'erreurs GeoIP

2. **Pipelines CSV** :
   - Nombre de documents indexés
   - Fréquence de mise à jour
   - Erreurs de parsing CSV

### Logs de Surveillance

```bash
# Surveillance en temps réel
tail -f /var/log/logstash/logstash-plain.log | grep -E "(o365-main|user-allowed|tenant-allowed)"

# Vérification des erreurs
grep -E "ERROR|WARN" /var/log/logstash/logstash-plain.log
```

## Maintenance et Optimisation

### Rotation des Fichiers CSV

Les fichiers CSV doivent être mis à jour régulièrement :

```bash
# Script de rotation automatique
#!/bin/bash
cp /var/data/csv/user_allowed.csv /var/data/csv/user_allowed.csv.backup
# Mise à jour du fichier...
# Logstash détectera automatiquement les changements
```

### Optimisation des Performances

1. **Mémoire JVM** : Minimum 4GB pour le pipeline principal
2. **Disque** : SSD recommandé pour les sincedb
3. **Réseau** : Latence <10ms vers Elasticsearch

### Gestion des Erreurs

```ruby
# Dans la configuration Logstash, ajout de gestion d'erreurs :
filter {
  if "_geoip_lookup_failure" in [tags] {
    mutate {
      add_field => { "geo_enrichment_failed" => "true" }
    }
  }
}
```

## Intégration avec Elasticsearch

### Index Templates

Les pipelines utilisent des index séparés pour une meilleure organisation :

- `logs-o365.audit-*` pour les événements O365
- `tenant_allowed_countries` pour les listes blanches par tenant
- `user_allowed_countries` pour les listes blanches par utilisateur-tenant

#### Séparation des Index

**Index `tenant_allowed_countries`** :
- Document ID : `{tenant}`
- Utilisation : Politiques d'enrichissement au niveau tenant
- Exemple : `contoso` → `["US", "GB", "DE", "FR"]`

**Index `user_allowed_countries`** :
- Document ID : `{tenant}|{user}`
- Utilisation : Politiques d'enrichissement granulaires par utilisateur
- Exemple : `contoso|alice@contoso.com` → `["US", "GB"]`

### Politiques d'Enrichissement

Les pipelines s'intègrent avec :
- `tenant-countries` : Enrichissement par tenant
- `user-tenant-countries` : Enrichissement par utilisateur-tenant

## Dépannage

### Problèmes Courants

1. **CSV non parsé** :
   ```bash
   # Vérifier le format et l'encodage UTF-8
   file /var/data/csv/user_allowed.csv
   ```

2. **Connexion TLS échouée** :
   ```bash
   # Tester la connectivité
   openssl s_client -connect elastic.example.com:9200 -CAfile /etc/logstash/certs/ca.crt
   ```

3. **Performance dégradée** :
   ```bash
   # Surveiller l'utilisation JVM
   curl -X GET "localhost:9600/_node/stats/jvm"
   ```

### Validation de Configuration

```bash
# Test de la syntaxe Logstash
/usr/share/logstash/bin/logstash --config.test_and_exit -f /etc/logstash/pipelines.d/

# Validation des pipelines
curl -X GET "localhost:9600/_node/pipelines"
```

## Références

- [Documentation Logstash Multi-Pipeline](https://www.elastic.co/guide/en/logstash/current/multiple-pipelines.html)
- [Configuration TLS Elastic Stack](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-basic-setup-https.html)
- [ECS Field Reference](https://www.elastic.co/guide/en/ecs/current/ecs-field-reference.html)
