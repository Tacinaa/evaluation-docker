# TP4 : Orchestration Avancée et Optimisation

## Contexte

Vous êtes responsable DevOps senior et devez préparer une application pour un déploiement en production. Ce TP couvre les aspects avancés : sécurité, optimisation, monitoring et bonnes pratiques de production.

---

## Exercice 1 : Sécurité et Secrets 

### 1.1 Docker Secrets avec Compose 

Reprenez l'architecture du TP3 et sécurisez les credentials en utilisant Docker Secrets.

Créez les fichiers de secrets :

```bash
echo "evaluser" > secrets/db_user.txt
echo "SuperSecretP@ss2024!" > secrets/db_password.txt
echo "evaldb" > secrets/db_name.txt
```

Modifiez le `docker-compose.yml` pour :

- Déclarer les secrets au niveau top-level
- Les injecter dans les services concernés
- Modifier les variables d'environnement pour lire depuis les fichiers secrets

**Exemple de lecture d'un secret dans un service :**

```yaml
environment:
  POSTGRES_PASSWORD_FILE: /run/secrets/db_password
```

### 1.2 Scan de vulnérabilités 

Utilisez Docker Scout ou Trivy pour scanner vos images :

```bash
# Avec Docker Scout (intégré)
docker scout cves eval-app:v1

# Ou avec Trivy
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image eval-app:v1
```

**Questions :**

- Combien de vulnérabilités critiques/hautes avez-vous trouvées ?
- Comment réduire le nombre de vulnérabilités ?
- Quelle image de base recommanderiez-vous ?

---

## Exercice 2 : Optimisation des images 

### 2.1 Analyse de la taille 

Analysez la composition de vos images avec `docker history` et `dive` :

```bash
# Installation de dive
docker run --rm -it \
  -v /var/run/docker.sock:/var/run/docker.sock \
  wagoodman/dive:latest eval-app:v1
```

**Questions :**

- Quel layer occupe le plus d'espace ?
- Quel est le ratio d'efficacité de votre image ?

### 2.2 Dockerfile optimisé 

Créez un Dockerfile ultra-optimisé pour l'API Node.js avec :

1. **Base minimale** : Utilisez `node:18-alpine` ou `gcr.io/distroless/nodejs`
2. **Multi-stage** : Séparer build et runtime
3. **Ordre des instructions** : Optimiser le cache
4. **Nettoyage** : Supprimer les fichiers inutiles

**Objectif** : Réduire la taille de l'image de 50% minimum par rapport à l'image de base.

Exemple de structure :

```dockerfile
# Stage 1: Dependencies
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

# Stage 2: Production
FROM node:18-alpine AS runner
WORKDIR /app

# Sécurité
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Copier uniquement le nécessaire
COPY --from=deps --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs app.js .

USER nodejs
EXPOSE 3000

CMD ["node", "app.js"]
```

### 2.3 .dockerignore 

Créez un fichier `.dockerignore` complet :
```
# Dépendances
node_modules
npm-debug.log

# Tests et développement
*.test.js
__tests__
coverage
.nyc_output

# Configuration IDE
.vscode
.idea
*.swp
*.swo

# Git
.git
.gitignore

# Docker
Dockerfile*
docker-compose*
.dockerignore

# Documentation
*.md
docs

# Environnement
.env*
*.local

# OS
.DS_Store
Thumbs.db
```

**Question :** Quel impact a le `.dockerignore` sur le temps de build et la taille du contexte ?

---

## Exercice 3 : Monitoring et Observabilité 

### 3.1 Stack de monitoring 

Ajoutez une stack de monitoring à votre `docker-compose.yml` :

**Prometheus** (`monitoring/prometheus.yml`) :

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

Services à ajouter :

```yaml
prometheus:
  image: prom/prometheus:latest
  volumes:
    - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml
    - prometheus_data:/prometheus
  ports:
    - "9090:9090"
  command:
    - '--config.file=/etc/prometheus/prometheus.yml'
    - '--storage.tsdb.path=/prometheus'

cadvisor:
  image: gcr.io/cadvisor/cadvisor:latest
  volumes:
    - /:/rootfs:ro
    - /var/run:/var/run:ro
    - /sys:/sys:ro
    - /var/lib/docker/:/var/lib/docker:ro
  ports:
    - "8081:8080"

node-exporter:
  image: prom/node-exporter:latest
  ports:
    - "9100:9100"

grafana:
  image: grafana/grafana:latest
  ports:
    - "3001:3000"
  volumes:
    - grafana_data:/var/lib/grafana
  environment:
    - GF_SECURITY_ADMIN_PASSWORD=admin
```

### 3.2 Dashboard Grafana 

- Accédez à Grafana (http://localhost:3001)
- Ajoutez Prometheus comme datasource
- Importez le dashboard "Docker and System Monitoring" (ID: 893)
- Créez une alerte si l'utilisation CPU d'un conteneur dépasse 80%

**Question :** Quelles métriques sont essentielles à surveiller en production ?

---

## Exercice 4 : Déploiement production-ready 

### 4.1 Profils Compose 

Utilisez les profils Docker Compose pour séparer les environnements :

```yaml
services:
  api:
    profiles: ["app", "all"]
    # ...

  db:
    profiles: ["app", "all"]
    # ...

  prometheus:
    profiles: ["monitoring", "all"]
    # ...

  grafana:
    profiles: ["monitoring", "all"]
    # ...
```

Commandes :
```bash
# Lancer uniquement l'app
docker compose --profile app up -d

# Lancer avec monitoring
docker compose --profile all up -d
```

### 4.2 Contraintes de ressources (2 points)

Ajoutez des limites de ressources à chaque service :

```yaml
services:
  api:
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 256M
        reservations:
          cpus: '0.25'
          memory: 128M
```

**Configuration recommandée :**
| Service | CPU Limit | Memory Limit |
|---------|-----------|--------------|
| API | 0.5 | 256M |
| PostgreSQL | 1.0 | 512M |
| Redis | 0.25 | 128M |
| Nginx | 0.25 | 64M |

### 4.3 Politique de redémarrage 

Configurez les politiques de redémarrage appropriées :

```yaml
services:
  api:
    restart: unless-stopped
    # ou en mode deploy
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
```

---

## Exercice Bonus : CI/CD Pipeline 

Créez un fichier `.github/workflows/docker.yml` pour automatiser :

```yaml
name: Docker CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build images
        run: docker compose build

      - name: Start services
        run: docker compose up -d

      - name: Wait for services
        run: |
          sleep 30
          curl --retry 10 --retry-delay 5 --retry-connrefused http://localhost/health

      - name: Run tests
        run: |
          curl -f http://localhost/api/users
          curl -f http://localhost/api/stats

      - name: Scan for vulnerabilities
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'eval-api:latest'
          severity: 'CRITICAL,HIGH'

      - name: Cleanup
        if: always()
        run: docker compose down -v
```

---

## Structure finale attendue

```
tp4/
├── docker-compose.yml
├── docker-compose.override.yml
├── .env
├── .dockerignore
├── secrets/
│   ├── db_user.txt
│   ├── db_password.txt
│   └── db_name.txt
├── api/
│   ├── Dockerfile
│   ├── Dockerfile.optimized
│   ├── app.js
│   └── package.json
├── db/
│   └── init.sql
├── nginx/
│   ├── nginx.conf
│   └── index.html
├── monitoring/
│   └── prometheus.yml
├── .github/
│   └── workflows/
│       └── docker.yml
└── reponses.md
```

