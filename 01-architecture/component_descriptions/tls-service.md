# Service TLS - Générateur de Certificats

## Vue d'ensemble

Le service TLS est un composant crucial du déploiement docker-elk qui s'exécute une seule fois pour générer tous les certificats X.509 nécessaires à la sécurisation des communications entre les composants de la pile ELK.

## Configuration TLS

### Rôle du Service TLS

- **Génération CA** : Création de l'autorité de certification racine
- **Certificats Services** : Génération des certificats pour chaque composant
- **Distribution** : Placement des certificats dans le volume Docker partagé
- **Validation** : Vérification de la cohérence des certificats

### Processus de Génération

```bash
# Commande de génération des certificats
docker compose up tls
```

#### Étapes Automatisées

1. **Vérification CA**
   ```bash
   if [[ ! -f config/certs/ca.zip ]]; then
     echo "Creating CA";
     bin/elasticsearch-certutil ca --silent --pem -out config/certs/ca.zip;
     unzip config/certs/ca.zip -d config/certs;
   fi;
   ```

2. **Génération Certificats Services**
   ```bash
   if [[ ! -f config/certs/certs.zip ]]; then
     echo "Creating certs";
     bin/elasticsearch-certutil cert --silent --pem -out config/certs/certs.zip \
       --in config/certs/instances.yml \
       --ca-cert config/certs/ca/ca.crt \
       --ca-key config/certs/ca/ca.key;
     unzip config/certs/certs.zip -d config/certs;
   fi;
   ```

3. **Attribution Permissions**
   ```bash
   chown -R root:root config/certs;
   find . -type d -exec chmod 755 \{\} \;;
   find . -type f -exec chmod 644 \{\} \;;
   ```

## Configuration instances.yml

### Fichier de Configuration

Le fichier `tls/instances.yml` définit les noms DNS et adresses IP pour lesquels générer les certificats :

```yaml
instances:
  - name: elasticsearch
    dns:
      - elasticsearch
      - es01
      - localhost
    ip:
      - 127.0.0.1
  
  - name: kibana
    dns:
      - kibana
      - localhost
    ip:
      - 127.0.0.1
      
  - name: fleet-server
    dns:
      - fleet-server
      - localhost
    ip:
      - 127.0.0.1
```

### Personnalisation

- **Environnements de production** : Ajouter les IP/DNS réels des serveurs
- **Accès externe** : Inclure les noms de domaine publics
- **Load balancers** : Ajouter les adresses des équilibreurs de charge

## Volume et Persistance

### Volume Docker

```yaml
volumes:
  certs:
    driver: local
```

### Structure des Certificats

```plaintext
certs/
├── ca/
│   ├── ca.crt              # Certificat CA racine
│   └── ca.key              # Clé privée CA
├── elasticsearch/
│   ├── elasticsearch.crt   # Certificat Elasticsearch
│   └── elasticsearch.key   # Clé privée Elasticsearch
├── kibana/
│   ├── kibana.crt         # Certificat Kibana
│   └── kibana.key         # Clé privée Kibana
└── fleet-server/
    ├── fleet-server.crt   # Certificat Fleet Server
    └── fleet-server.key   # Clé privée Fleet Server
```

## Maintenance et Re-génération

### Re-génération Complète

```bash
# Suppression des certificats existants
find tls/certs -name ca -prune -or -type d -mindepth 1 -exec rm -rfv {} +

# Re-génération
docker compose up tls
```

### Bonnes Pratiques

1. **Sauvegarde** : Backup du volume `certs` avant re-génération
2. **Rotation** : Re-génération trimestrielle des certificats
3. **Monitoring** : Surveillance de l'expiration des certificats
4. **Testing** : Validation des certificats après génération

### Dépannage

#### Problèmes Courants

- **Volume non persistant** : Vérifier la configuration Docker volumes
- **Permissions** : Problèmes d'accès aux certificats par les services
- **DNS/IP manquants** : Erreurs de validation lors des connexions

#### Validation

```bash
# Vérification du certificat CA
openssl x509 -in tls/certs/ca/ca.crt -text -noout

# Vérification d'un certificat service
openssl x509 -in tls/certs/elasticsearch/elasticsearch.crt -text -noout
```

## Sécurité

### Protection des Clés

- **Accès restreint** : Permissions limitées sur le volume Docker
- **Chiffrement** : Clés privées protégées par permissions système
- **Audit** : Traçabilité des accès aux certificats

### Rotation de Sécurité

- **Fréquence** : Rotation tous les 6 mois minimum
- **Urgence** : Re-génération immédiate en cas de compromission
- **Validation** : Test de tous les services après rotation
