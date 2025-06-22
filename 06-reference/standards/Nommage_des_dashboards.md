# Convention de Nommage des Dashboards

## Objectif

Garantir une organisation claire, cohérente et évolutive des dashboards de sécurité et de monitoring dans l’ensemble du projet ELK.

## Structure Générale

Le nom d’un dashboard doit refléter :

- Le domaine ou la plateforme concernée
- La fonction ou la métrique principale
- Le contexte ou la période (optionnel)

Format recommandé :

```
{Plateforme/Domaine} – {Fonction/Métrique} – (Contexte Optionnel)
```

## Exemples

- `Infrastructure – Accès Utilisateurs – (24h)`
- `O365 – Email Security – (Phishing)`
- `Infrastructure – Compliance – (SOC2)`
- `O365 – Teams Overview – (By Department)`
- `Monitoring – Performance – (CPU Usage)`

## Règles de Nommage

1. **Plateforme/Domaine** :
   - Toujours commencer par la plateforme ou le domaine (ex : Infrastructure, O365, Monitoring, Azure, etc.)
   - Utiliser des noms explicites et homogènes
2. **Fonction/Métrique** :
   - Décrire la fonction principale ou la métrique clé du dashboard (ex : Accès, Compliance, Email Security, Performance)
   - Privilégier des termes métier compréhensibles par tous
3. **Contexte Optionnel** :
   - Préciser la période, le périmètre ou le filtre principal si pertinent (ex : 24h, By Tennant, )
   - Mettre entre parenthèses
4. **Langue** :
   - Utiliser l’anglais pour les termes techniques ou internationaux, le français pour les usages internes si besoin
5. **Lisibilité** :
   - Pas d’abréviations non standards
   - Pas de caractères spéciaux (sauf parenthèses pour le contexte)
   - Capitaliser chaque mot principal

## Bonnes Pratiques

- Vérifier l’unicité du nom dans la plateforme concernée
- Mettre à jour le nom si le périmètre du dashboard évolue
- Documenter le nommage dans le README du dossier dashboards
- Aligner le nom du fichier, du dashboard Kibana et de la documentation

## Exemples de Mauvais Noms

- `dash1`
- `infra_access`
- `O365-Phishing`
- `perf.cpu`

## Exemples de Bons Noms

- `O365 – Email Security – (Phishing)`
- `Infrastructure – Compliance – (SOC2)`
- `Monitoring – Performance – (CPU Usage)`
