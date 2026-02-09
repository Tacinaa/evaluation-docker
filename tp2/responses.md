## Exercice 1 : Dockerfile basique 

### 1.2 Build et test

**Commandes utilisées :**

```bash
docker build -t eval-app:v1 .
docker image ls
docker run -d -p 3001:3000 --name eval-node eval-app:v1
curl http://localhost:3001
docker image ls eval-app
docker history eval-app:v1
```

>Le test avec curl retourne bien une réponse JSON.

>Taille de l'image : environ 181MB.

**Questions :**
- Combien de layers l'image contient-elle ?
```text
13.
```
- Quelle commande permet de voir l'historique des layers ?
```bash
docker history eval-app:v1
```

---

## Exercice 2 : Optimisation du Dockerfile

### 2.1 Cache des dépendances 

```text
Cette approche est plus efficace et optimisée car si le package.json ne change pas, la couche npm install est réutilisée et les dépendances ne sont pas réinstallées à chaque build.
```

### 2.2 Multi-stage build

**Commandes utilisées :**

```bash
docker build -f Dockerfile.multistage -t eval-app:v2 .
docker image ls eval-app
```

```text
Taille de la v1 : 181MB
Taille de la v2 : 158MB

L'image multi-stage n'est pas plus légère car l'application est très simple et contient peu de dépendances.
Dans un projet plus complexe, le multi-stage permettrait une réduction plus importante de la taille.
```

### 2.3 Utilisateur non-root 

**Commandes utilisées :**

```bash
docker build -f Dockerfile.secure -t eval-app:v3 .
docker run -d -p 3002:3000 eval-app:v3
curl http://localhost:3002
docker ps
docker exec -it trusting_dhawan  whoami
```

> Résultat : appuser

**Question :** Pourquoi est-ce important pour la sécurité ?
```text
C'est important car en cas de faille, l'attaquant ne dispose pas des privilèges root sur le conteneur.
```

---

## Exercice 3 : Arguments et variables

### 3.2 Build avec arguments

**Commandes utilisées :**

```bash
docker build -f Dockerfile.env --build-arg NODE_VERSION=20 -t eval-app:v3-node20 .
docker run -d -p 3003:3000 eval-app:v3-node20

docker run -d -p 3004:3000 -e APP_ENV=development eval-app:v3-node20
curl http://localhost:3004
```

>On a bien "environment":"development".
> 
> **Questions :**
- Quelle est la différence entre ARG et ENV ?
```text
ARG est disponible uniquement lors du build de l'image tandis que ENV est accessible dans le conteneur au runtime.
```
- Comment passer une variable d'environnement au `docker run` ?
```bash
  docker run -e NOM_VARIABLE=valeur image
```

---

## Exercice 4 : Application Python

### 4.2 HEALTHCHECK

**Commandes utilisées :**

```bash
docker build -t eval-flask:v1 .
docker run -d -p 5001:5000 eval-flask:v1
docker ps
```

>On a bien (healthy) en status.


### Notes :
>Plusieurs Dockerfiles ont été conservés afin de montrer l’évolution et les différentes optimisations.
