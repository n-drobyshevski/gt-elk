# Dashboards Organization

## Structure du Répertoire

```plaintext
dashboards/
├── o365/                           # Dashboards Office 365
│   ├── audit/                      # Audit et conformité
│   ├── email/                      # Métriques Exchange
│   ├── sharepoint/                 # Activité SharePoint
│   └── teams/                      # Usage Teams
├── performance/                    # Monitoring des performances
│   ├── system/                     # Métriques système et OS
│   ├── applications/              # Performance des applications
│   └── infrastructure/            # Infrastructure & conteneurs
├── security/                       # Sécurité
│   ├── access/                     # Contrôle d'accès et authentification
│   ├── compliance/                 # Conformité et audit
│   └── overview/                   # Vue d'ensemble de la sécurité
└── shared/                         # Éléments partagés
    ├── visualizations/             # Visualisations communes
    └── templates/                  # Templates de dashboards
```

## Conventions de Nommage

### Format Standard

```
{Domain} – {Function/Metric} – (Optional Context)
```

Principes clés :

- Initial Caps pour tous les mots significatifs
- Maximum 80 caractères
- Langue unifiée (anglais)
- Pas de numéros de version dans le titre (gérés via Git)
- Tags Kibana pour le filtrage

### Structure du Nom

1. **Domaine** (obligatoire)
   
   - Security
   - Infrastructure
   - Business
   - Office365
   - System

2. **Fonction/Métrique** (obligatoire)
   
   - Failed Logins
   - CPU Usage
   - Mail Flow
   - User Activity

3. **Contexte** (optionnel)
   
   - (per Host)
   - (Daily)
   - (Top 10)

### Exemples

- `Security – Failed Logins – (Last 24h)`
- `Infrastructure – Cluster Health – (per Node)`
- `Office365 – Mail Flow – (Exchange Online)`
- `System – Resource Usage – (Docker Containers)`

### Tags Kibana

Chaque dashboard doit inclure les tags suivants :

- `team:<team_id>` - Équipe responsable
- `env:<environment>` - Environnement (prod/dev/test)
- `domain:<domain>` - Domaine fonctionnel:
  - security: sécurité, authentification, conformité
  - infrastructure: composants système, clusters, réseau
  - o365: tout ce qui concerne Microsoft 365
  - compliance: aspects réglementaires et audit
  - platform: composants de la plateforme ELK
- `type:<dashboard_type>` - Type de dashboard:
  - monitoring: surveillance en temps réel des systèmes et performances
  - analytics: analyse des tendances et insights métier
  - audit: traçabilité des actions et conformité réglementaire

### Gestion des Abréviations

Les abréviations doivent être :

- Documentées dans le fichier `glossary/README.md`
- Utilisées de manière cohérente
- Limitées aux standards de l'industrie (CPU, RAM, I/O)

## Gestion des Versions

### Sauvegarde des Dashboards

1. Exporter régulièrement via l'API Kibana
2. Stocker les fichiers `.ndjson` dans Git
3. Documenter les changements dans CHANGELOG.md

### Exemple de Commande d'Export

```bash
curl -X POST "localhost:5601/api/saved_objects/_export" -H 'kbn-xsrf: true' -H 'Content-Type: application/json' -d'
{
  "type": ["dashboard"],
  "includeReferencesDeep": true
}'
```

## Procédures de Maintenance

### Revue Périodique

- Revue mensuelle des dashboards
- Archivage après 90 jours sans utilisation
- Validation des performances
- Mise à jour de la documentation

### Performance

- Limiter les timeranges par défaut
- Optimiser les requêtes
- Surveiller l'impact sur Elasticsearch

## Documentation des Dashboards

### Template de Documentation

```markdown
# [Nom du Dashboard]

## Objectif
[Description brève du but du dashboard]

## Sources de Données
- Index pattern : [pattern]
- Refresh rate : [interval]

## Dépendances
- [Liste des visualisations requises]
- [Transformations nécessaires]

## Accès Requis
- Rôle : [role_name]
- Index : [index_pattern]

## Maintenance
- Propriétaire : [nom]
- Dernière mise à jour : [date]
- Version : [X.Y]
```

## Best Practices

### Création de Dashboard

1. Commencer par un template standard
2. Limiter le nombre de visualisations (<15)
3. Organiser logiquement les panneaux
4. Inclure une documentation claire

### Optimisation

1. Utiliser des agrégations efficaces
2. Implémenter des filtres pertinents
3. Configurer des refresh rates appropriés
4. Mettre en cache les résultats si possible

## Contrôle d'Accès

### Niveaux d'Accès

- **Admin** : Création/modification/suppression
- **Analyst** : Création/modification
- **Viewer** : Lecture seule

### Configuration RBAC

```yaml
# Exemple de rôle Kibana
role_name: "dashboard_viewer"
kibana:
  feature:
    dashboard: ["read"]
    discover: ["read"]
```

## Procédures d'Import/Export

### Export de Dashboard

1. Sélectionner le dashboard dans Kibana
2. Utiliser "Share -> Export"
3. Sauvegarder le fichier .ndjson
4. Mettre à jour le repository

### Import de Dashboard

1. Aller dans Stack Management
2. Saved Objects -> Import
3. Sélectionner le fichier .ndjson
4. Résoudre les conflits si nécessaires

## Support et Contact

### En Cas de Problème

1. Vérifier les logs Kibana
2. Consulter la documentation technique
3. Contacter l'équipe support

### Ressources

- Documentation Kibana
- Guide des best practices
- Repository Git du projet
