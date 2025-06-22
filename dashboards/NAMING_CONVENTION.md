# Convention de Nommage des Dashboards

## Format Standard

```plaintext
{Domain} – {Function/Metric} – (Optional Context)
```

## Principes clés

- Initial Caps pour tous les mots significatifs
- Maximum 80 caractères
- Langue unifiée (anglais)
- Pas de numéros de version dans le titre (gérés via Git)
- Tags Kibana pour le filtrage

## Structure du Nom

### 1. Domaine (obligatoire)

- Security
- Infrastructure
- Business
- Office365
- System

### 2. Fonction/Métrique (obligatoire)

- Failed Logins
- CPU Usage
- Mail Flow
- User Activity

### 3. Contexte (optionnel)

- (per Host)
- (Daily)
- (EMEA)
- (Top 10)

## Exemples

- `Security – Failed Logins – (Last 24h)`
- `Infrastructure – Cluster Health – (per Node)`
- `Office365 – Mail Flow – (Exchange Online)`
- `System – Resource Usage – (Docker Containers)`

## Tags Kibana

Chaque dashboard doit inclure les tags suivants :

- `team:<team_id>` - Équipe responsable
- `env:<environment>` - Environnement (prod/dev/test)
- `domain:<domain>` - Domaine fonctionnel
- `type:<dashboard_type>` - Type de dashboard (monitoring/analytics/audit)

## Gestion des Abréviations

Les abréviations doivent être :

- Documentées dans le fichier `glossary/GLOSSARY.md`
- Utilisées de manière cohérente
- Limitées aux standards de l'industrie (CPU, RAM, I/O)

## Migration des Anciens Noms

Pour migrer les anciens noms de dashboards vers le nouveau format :

1. Créer une copie du dashboard avec le nouveau nom
2. Mettre à jour les tags
3. Ajouter une redirection dans l'ancien dashboard
4. Planifier la suppression de l'ancien dashboard après 30 jours
