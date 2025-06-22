# Dashboards Système

Ce répertoire contient les dashboards pour la surveillance système de notre infrastructure ELK.

## Structure

### `/elasticsearch`

- Performance du cluster
- État des nœuds
- Métriques JVM
- Santé des index

### `/containers`

- Utilisation des ressources Docker
- État des conteneurs
- Métriques réseau
- Logs système

### `/infrastructure`

- Métriques des VM
- Utilisation des ressources
- Performance réseau
- État du stockage

## Conventions de Nommage

Format : `PROD-SYS-[Composant]-[Métrique]-v[Version]`

Exemples :

- `PROD-SYS-ES-ClusterHealth-v1.0`
- `PROD-SYS-Docker-ResourceUsage-v2.1`
- `PROD-SYS-VM-Performance-v1.2`
