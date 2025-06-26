# Exemples de Fichiers CSV pour Listes Blanches O365

## Format des Fichiers CSV

### 1. Fichier `user_allowed.csv`

**Structure** : Listes blanches par combinaison tenant-utilisateur

```csv
tenant_user,allowed
contoso|alice@contoso.com,US|GB|DE
contoso|bob@contoso.com,US|FR
fabrikam|charlie@fabrikam.com,FR|BE|LU
northwind|diana@northwind.com,CA|US
contoso|eve@contoso.com,JP|KR|SG
```

**Description des champs** :
- `tenant_user` : Clé composite `{tenant_id}|{user_email}`
- `allowed` : Liste des codes pays ISO 3166-1 alpha-2 séparés par `|`

### 2. Fichier `tenant_allowed.csv`

**Structure** : Listes blanches par tenant

```csv
tenant,allowed
contoso,US|GB|DE|FR|ES
fabrikam,FR|BE|LU|NL|DE
northwind,CA|US|MX
acme,AU|NZ|SG|JP
```

**Description des champs** :
- `tenant` : Identifiant du tenant O365
- `allowed` : Liste des codes pays ISO 3166-1 alpha-2 séparés par `|`

## Exemples de Documents Elasticsearch Résultants

### Index `user_allowed_countries`

```json
{
  "_id": "contoso|alice@contoso.com",
  "_source": {
    "tenant_user": "contoso|alice@contoso.com",
    "tenant": {
      "id": "contoso"
    },
    "user": {
      "name": "alice@contoso.com"
    },
    "allowed": ["US", "GB", "DE"],
    "@timestamp": "2024-01-15T10:30:00.000Z",
    "import_source": "user_allowed_csv"
  }
}
```

### Index `tenant_allowed_countries`

```json
{
  "_id": "contoso",
  "_source": {
    "tenant": {
      "id": "contoso"
    },
    "allowed": ["US", "GB", "DE", "FR", "ES"],
    "@timestamp": "2024-01-15T10:30:00.000Z",
    "import_source": "tenant_allowed_csv"
  }
}
```

## Validation des Données

### Règles de Validation

1. **Format tenant_user** :
   - Doit contenir exactement un caractère `|` 
   - Partie tenant ne doit pas être vide
   - Partie utilisateur doit être un email valide

2. **Codes pays** :
   - Codes ISO 3166-1 alpha-2 uniquement (2 caractères)
   - Séparés par `|` sans espaces
   - Tous en majuscules

3. **Encodage** :
   - UTF-8 obligatoire
   - Pas de BOM (Byte Order Mark)

### Script de Validation

```bash
#!/bin/bash
# validate_csv.sh - Script de validation des fichiers CSV

validate_user_csv() {
    local file="$1"
    echo "Validation de $file..."
    
    # Vérification du format tenant_user
    awk -F',' 'NR>1 {
        if ($1 !~ /^[^|]+\|[^@]+@[^@]+\.[^@]+$/) {
            print "Ligne " NR ": Format tenant_user invalide - " $1
        }
    }' "$file"
    
    # Vérification des codes pays
    awk -F',' 'NR>1 {
        split($2, countries, "|")
        for (i in countries) {
            if (length(countries[i]) != 2 || countries[i] !~ /^[A-Z]{2}$/) {
                print "Ligne " NR ": Code pays invalide - " countries[i]
            }
        }
    }' "$file"
}

validate_tenant_csv() {
    local file="$1"
    echo "Validation de $file..."
    
    # Vérification du format tenant
    awk -F',' 'NR>1 {
        if ($1 == "" || $1 ~ /\|/) {
            print "Ligne " NR ": Format tenant invalide - " $1
        }
    }' "$file"
    
    # Vérification des codes pays
    awk -F',' 'NR>1 {
        split($2, countries, "|")
        for (i in countries) {
            if (length(countries[i]) != 2 || countries[i] !~ /^[A-Z]{2}$/) {
                print "Ligne " NR ": Code pays invalide - " countries[i]
            }
        }
    }' "$file"
}

# Utilisation
validate_user_csv "/var/data/csv/user_allowed.csv"
validate_tenant_csv "/var/data/csv/tenant_allowed.csv"
```

## Gestion des Mises à Jour

### Fréquence de Mise à Jour

- **user_allowed.csv** : Mise à jour quotidienne ou à la demande
- **tenant_allowed.csv** : Mise à jour hebdomadaire ou lors de changements organisationnels

### Processus de Déploiement

1. **Validation locale** :
   ```bash
   ./validate_csv.sh
   ```

2. **Sauvegarde de l'existant** :
   ```bash
   cp /var/data/csv/user_allowed.csv /var/data/csv/backups/user_allowed_$(date +%Y%m%d_%H%M%S).csv
   ```

3. **Déploiement** :
   ```bash
   cp user_allowed_new.csv /var/data/csv/user_allowed.csv
   ```

4. **Vérification Logstash** :
   ```bash
   tail -f /var/log/logstash/logstash-plain.log | grep "user-allowed"
   ```

### Monitoring des Changements

```bash
# Script de surveillance des changements
inotifywait -m /var/data/csv/ -e modify -e create |
while read path action file; do
    echo "$(date): $file a été $action dans $path"
    # Optionnel : déclencher une validation automatique
done
```

## Codes Pays Supportés

### Codes ISO 3166-1 Alpha-2 Couramment Utilisés

| Code | Pays | Code | Pays |
|------|------|------|------|
| US | États-Unis | GB | Royaume-Uni |
| FR | France | DE | Allemagne |
| ES | Espagne | IT | Italie |
| CA | Canada | AU | Australie |
| JP | Japon | KR | Corée du Sud |
| SG | Singapour | BE | Belgique |
| NL | Pays-Bas | LU | Luxembourg |
| CH | Suisse | AT | Autriche |

### Référence Complète

Pour la liste complète des codes pays ISO 3166-1 alpha-2, consulter : [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)

## Dépannage

### Problèmes Courants

1. **CSV non traité** :
   - Vérifier l'encodage UTF-8
   - Contrôler les permissions de fichier
   - Valider la syntaxe CSV

2. **Documents non indexés** :
   - Vérifier les logs Logstash
   - Contrôler la connectivité Elasticsearch
   - Valider les mappings d'index

3. **Enrichissement échoué** :
   - Vérifier les politiques d'enrichissement
   - Contrôler les noms de champs
   - Valider les clés de lookup
