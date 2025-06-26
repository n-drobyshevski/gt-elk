# Exemples de Fichiers CSV pour les Listes Blanches Géographiques O365

## Fichier user_allowed.csv

```csv
tenant_user,allowed
contoso|alice@contoso.com,US|GB|FR
contoso|bob@contoso.com,US|DE
contoso|admin@contoso.com,US|GB|FR|DE|ES|IT
fabrikam|john@fabrikam.com,GB|FR|ES
fabrikam|sarah@fabrikam.com,GB|IT
```

## Fichier tenant_allowed.csv

```csv
tenant,allowed
contoso,US|GB|FR|DE|ES
fabrikam,GB|FR|ES|IT
tailspintoys,US|CA|MX
northwind,US|GB|FR|DE
```

## Format de Données

### Structure tenant_user
- **Format** : `tenant|email@domain.com`
- **Exemple** : `contoso|alice@contoso.com`
- **Usage** : Clé unique pour les politiques utilisateur spécifiques

### Structure allowed
- **Format** : Codes pays ISO 3166-1 alpha-2 séparés par `|`
- **Exemple** : `US|GB|FR|DE`
- **Codes supportés** : US, GB, FR, DE, ES, IT, CA, MX, JP, AU, etc.

## Validation des Données

### Règles de Validation

1. **tenant_user** :
   - Doit contenir exactement un caractère `|`
   - Partie tenant : caractères alphanumériques, tirets, underscores
   - Partie email : format email valide

2. **tenant** :
   - Caractères alphanumériques, tirets, underscores uniquement
   - Longueur minimale : 3 caractères
   - Longueur maximale : 50 caractères

3. **allowed** :
   - Codes pays en majuscules uniquement
   - Format ISO 3166-1 alpha-2 (2 caractères)
   - Séparateur `|` entre les codes
   - Maximum 50 pays par entrée

### Exemples d'Erreurs Communes

```csv
# INCORRECT - Format email invalide
contoso|alice.contoso.com,US|GB

# INCORRECT - Code pays invalide
contoso|bob@contoso.com,USA|GBR

# INCORRECT - Caractères spéciaux dans tenant
contos@|alice@contoso.com,US|GB

# CORRECT
contoso|alice@contoso.com,US|GB|FR
```

## Cas d'Usage

### 1. Restriction Géographique par Utilisateur
Un utilisateur VIP peut accéder depuis plus de pays que la politique tenant globale :

```csv
# tenant_allowed.csv
contoso,US|GB

# user_allowed.csv  
contoso|ceo@contoso.com,US|GB|FR|DE|JP
```

### 2. Restriction Stricte pour Utilisateurs Spécifiques
Un utilisateur contractuel avec accès limité :

```csv
# tenant_allowed.csv
contoso,US|GB|FR|DE

# user_allowed.csv
contoso|contractor@contoso.com,US
```

### 3. Politique par Défaut Tenant
Aucune règle utilisateur spécifique, utilise la politique tenant :

```csv
# tenant_allowed.csv
fabrikam,GB|FR|ES

# user_allowed.csv
# (aucune entrée pour les utilisateurs fabrikam)
```

---

**Note** : Les fichiers CSV doivent être encodés en UTF-8 et utiliser des fins de ligne Unix (LF).
