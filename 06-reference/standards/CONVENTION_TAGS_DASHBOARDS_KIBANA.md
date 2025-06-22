# Standard de Tagging pour les Dashboards Kibana

## Objectif

Définir une convention claire et homogène pour l’utilisation des tags dans les dashboards Kibana, afin de faciliter la recherche, la gestion, la gouvernance et l’automatisation dans l’environnement ELK_GT.

## Pourquoi utiliser des tags ?

- Filtrer et retrouver rapidement les dashboards par thématique, usage ou criticité
- Faciliter l’automatisation (CI/CD, reporting, alerting)
- Améliorer la gouvernance et la documentation
- Permettre des vues transverses (ex : tous les dashboards « conformité » ou « production »)

## Règles Générales

- Les tags doivent être explicites, concis et en anglais (sauf usage interne spécifique)
- Utiliser uniquement des caractères alphanumériques et le tiret court (-) comme séparateur
- Pas d’espaces, pas de caractères spéciaux, pas d’accents
- Toujours en minuscules
- Privilégier des tags métier ou techniques compréhensibles par tous
- Limiter à 3-5 tags principaux par dashboard

## Catégories de Tags Recommandées

1. **Domaine/Plateforme**
   - infrastructure, o365, azure, aws, network, endpoint
2. **Fonction/Sujet**
   - access, compliance, threat, performance, monitoring, audit, security, operations
3. **Criticité/Contexte**
   - production, staging, critical, gdpr, pci, dev
4. **Type de Donnée**
   - logs, metrics, traces, apm, siem

## Exemples de Tags

- infrastructure, access, production
- o365, compliance, gdpr
- monitoring, performance, devops
- security, threat, soc
- azure, audit, critical

## Bonnes Pratiques

- Toujours documenter les tags utilisés 
- Harmoniser les tags entre dashboards similaires
- Mettre à jour les tags si le périmètre du dashboard évolue
- Éviter les doublons ou les variantes inutiles (ex : « prod » vs « production »)

## Exemples de Mauvais Tags

- infra, acc, prod (trop courts ou ambigus)
- sécurité (préférer « security »)
- logs-metrics (préférer deux tags séparés)

## Exemples de Bons Tags

- infrastructure, access, production
- o365, compliance, gdpr
- monitoring, performance, devops
