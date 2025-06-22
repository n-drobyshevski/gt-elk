# Dashboards Sécurité

Ce répertoire contient les dashboards pour la surveillance et l'analyse de la sécurité.

## Structure

### `/access`

- Patterns d'authentification
- Activité des utilisateurs
- Gestion des accès
- Connexions suspectes

### `/compliance`

- Tableaux de bord de conformité
- Audit de sécurité
- Rapports réglementaires
- Contrôles d'accès

### `/overview`

- Vue d'ensemble de la sécurité
- Métriques clés
- État global de la sécurité
- Tendances et anomalies

## Note Importante

Pour les dashboards liés à la détection des menaces et aux alertes de sécurité, veuillez vous référer au répertoire `detection-rules/`. Cette séparation permet une meilleure organisation entre :

- Les règles de détection (dans `detection-rules/`)
- Les visualisations de sécurité (dans ce répertoire)

## Conventions de Nommage

Format : `{Domain} – {Function/Metric} – (Optional Context)`

Exemples :

- `Security – Authentication Patterns – (Last 24h)`
- `Security – Compliance Status – (SOC2)`
- `Security – Access Overview – (By Department)`
