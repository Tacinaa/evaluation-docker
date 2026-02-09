### 1.2 Validation 

**Commandes utilisées :**
```bash
docker compose up -d
docker compose ps
```
>Le port a été changé en 8081 pour éviter les conflits.

**Requête SQL**
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
```

>La table apparait correctement.

**Questions :**

- Comment voir les logs des deux services en temps réel ?
```bash
docker compose logs -f
```
- Comment accéder au CLI PostgreSQL depuis l'extérieur ?
```bash
docker exec -it eval-db psql -U evaluser -d evaldb
```

---

## Exercice 2 : Application multi-tiers

### 2.2 Réseau personnalisé

**Question :** Quel est l'avantage d'un réseau personnalisé par rapport au réseau par défaut ?
```text
Un réseau personnalisé permet d’isoler les services et de faciliter la communication entre conteneurs via leurs noms sans exposer tous les ports à l’extérieur.
```

### 2.3 Script d'initialisation DB

**Comandes utilisées :**

```bash
docker compose down -v
docker compose up -d --build
```

---

## Exercice 3 : Reverse Proxy Nginx

### 3.1 Configuration Nginx 

>J'ai remplacé le port 80 par 82.

```bash
docker compose down
docker compose up -d --build
```

### 3.2 Test complet

On a bien :
```json
{
  "userCount": "3",
  "timestamp": "2026-02-09T13:39:02.485Z",
  "source": "cache"
}
```

**Questions :**

- Comment vérifier que Redis met bien en cache les données ?
```text
Le premier click sur Stats doit montrer "source: database" et le second "source: database"
```
- Que se passe-t-il si vous arrêtez le service Redis ?
```bash
docker stop eval-redis
```
```text
Les boutons ne renvoient plus rien.
```

## Exercice 4 : Environnements 

### 4.2 Override pour développement

**Commandes utilisées :**

```bash
docker compose down -v
docker compose up -d --build
```

>Le montage du code source (./api:/app) écrasait le dossier /app du conteneur donc j'ai dû ajouter un volume anonyme /app/node_modules pour conserver les dépendances installées
dans l’image.

>Pour localhost:82/health on a bien -> {"status":"healthy","service":"api"}