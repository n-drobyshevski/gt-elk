# Service Setup - Initialisation Utilisateurs

## Vue d'ensemble

Le service Setup est responsable de l'initialisation sécurisée des utilisateurs Elasticsearch lors du premier déploiement du cluster. Il s'exécute après la génération des certificats TLS et configure l'authentification pour tous les composants.

## Fonctionnalités Principales

### Initialisation Utilisateurs

- **elastic** : Superutilisateur pour administration générale
- **kibana_system** : Utilisateur système dédié pour Kibana
- **logstash_internal** : Utilisateur système pour connexions Logstash
- **beats_system** : Utilisateur pour agents Beats (Metricbeat, etc.)

### Configuration TLS

```yaml
environment:
  ELASTIC_PASSWORD: ${ELASTIC_PASSWORD}
  KIBANA_PASSWORD: ${KIBANA_PASSWORD}
  LOGSTASH_INTERNAL_PASSWORD: ${LOGSTASH_INTERNAL_PASSWORD}
```

## Processus d'Initialisation

### Séquence de Déploiement

```bash
# 1. Génération certificats TLS
docker compose up tls

# 2. Initialisation utilisateurs
docker compose up setup

# 3. Démarrage des services
docker compose up
```

### Validation du Setup

Le service Setup vérifie :

- **Connectivité Elasticsearch** : Accès TLS au cluster
- **Création utilisateurs** : Existence et permissions correctes
- **Mots de passe** : Application des credentials configurés

## Configuration Utilisateurs

### Variables d'Environnement

```yaml
# .env file configuration
ELASTIC_PASSWORD=changeme
KIBANA_SYSTEM_PASSWORD=changeme
LOGSTASH_INTERNAL_PASSWORD=changeme
BEATS_SYSTEM_PASSWORD=changeme
```

### Permissions par Utilisateur

#### elastic (Superuser)

- **Cluster** : Tous privilèges
- **Indices** : Lecture/écriture tous index
- **Usage** : Administration générale, configuration système

#### kibana_system

- **Cluster** : Monitor, manage_index_templates
- **Indices** : .kibana*, .reporting*, .monitoring*
- **Usage** : Interface Kibana, gestion visualisations

#### logstash_internal

- **Cluster** : Monitor, manage_index_templates
- **Indices** : Écriture sur logstash-*, o365-*
- **Usage** : Ingestion données via Logstash

#### beats_system

- **Cluster** : Monitor
- **Indices** : Écriture sur metricbeat-*, filebeat-*
- **Usage** : Agents Beats pour monitoring

## Sécurité et Bonnes Pratiques

### Gestion des Mots de Passe

#### Changement Initial

```bash
# Reset password elastic user
docker compose exec elasticsearch bin/elasticsearch-reset-password \
  --batch --user elastic --url https://localhost:9200

# Reset password kibana_system
docker compose exec elasticsearch bin/elasticsearch-reset-password \
  --batch --user kibana_system --url https://localhost:9200

# Reset password logstash_internal
docker compose exec elasticsearch bin/elasticsearch-reset-password \
  --batch --user logstash_internal --url https://localhost:9200
```

#### Mise à Jour des Configurations

Après changement des mots de passe :

1. **Fichier .env** : Mettre à jour les variables d'environnement
2. **Redémarrage services** : `docker compose up -d logstash kibana`

### Authentification TLS

#### Certificats Requis

- **CA Certificate** : `/usr/share/elasticsearch/config/certs/ca/ca.crt`
- **Client Authentication** : Validation mutuelle pour sécurité maximale

#### Validation Connexion

```bash
# Test connexion TLS avec certificat
curl -X GET "https://localhost:9200/_cluster/health?pretty" \
  --cacert tls/certs/ca/ca.crt \
  -u elastic:${ELASTIC_PASSWORD}
```

## Maintenance et Re-exécution

### Re-initialisation Complète

```bash
# Re-exécution du setup
docker compose up setup
```

### Cas d'Usage

- **Nouveau déploiement** : Première initialisation
- **Reset des mots de passe** : Réinitialisation complète
- **Ajout d'utilisateurs** : Extension des permissions

### Monitoring du Setup

#### Logs de Débogage

```bash
# Consultation des logs setup
docker compose logs setup

# Vérification état utilisateurs
curl -X GET "https://localhost:9200/_security/user" \
  --cacert tls/certs/ca/ca.crt \
  -u elastic:${ELASTIC_PASSWORD}
```

## Dépannage

### Problèmes Courants

#### Échec de Connexion Elasticsearch

- **Cause** : Cluster non démarré ou certificats TLS invalides
- **Solution** : Vérifier `docker compose logs elasticsearch`

#### Utilisateurs Non Créés

- **Cause** : Variables d'environnement manquantes
- **Solution** : Valider configuration .env

#### Erreurs de Permissions

- **Cause** : Rôles mal configurés
- **Solution** : Re-exécution du setup avec logs debug

### Validation Post-Setup

```bash
# Test de chaque utilisateur
curl -X GET "https://localhost:9200/_security/_authenticate" \
  --cacert tls/certs/ca/ca.crt \
  -u elastic:${ELASTIC_PASSWORD}

curl -X GET "https://localhost:9200/_security/_authenticate" \
  --cacert tls/certs/ca/ca.crt \
  -u kibana_system:${KIBANA_SYSTEM_PASSWORD}
```

## Intégration avec l'Architecture

### Dépendances

- **TLS Service** : Doit être exécuté avant Setup
- **Elasticsearch** : Cluster disponible pour initialisation
- **Volume certs** : Accès aux certificats TLS

### Impact sur les Services

- **Kibana** : Utilise kibana_system pour connexion
- **Logstash** : Utilise logstash_internal pour sortie
- **Fleet Server** : Utilise elastic credentials pour gestion
- **Metricbeat** : Utilise beats_system pour ingestion métriques
