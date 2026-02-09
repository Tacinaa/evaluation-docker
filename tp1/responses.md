## Exercice 1 : Premiers pas

### 1.1 Vérification de l'installation

**Commande utilisée :**

```bash
docker --version
docker compose version
```

**Résultat :**

```
Docker version 29.1.3, build f52814d
Docker Compose version v2.40.3-desktop.1
```

### 1.2 Téléchargement d'images 

**Questions :**
- Quelle est la taille de chaque image ?
```text
La taille de l'image nginx:alpine est de 93.4MB et celle de redis:7-alpine est de 61.2MB.
```
- Pourquoi utilise-t-on des images `alpine` ?
```text
Elles sont plus légères que les images classiques, ce qui réduit le temps de téléchargement, l’espace disque utilisé et la surface d’attaque en sécurité.
```

### 1.3 Liste des images 
**Questions :**

- Quelle commande avez-vous utilisée ?
```bash
docker image ls
docker image ls | measure
```
- Combien d'images sont présentes ?
```text
25 images sont présentes.
```

## Exercice 2 : Gestion des conteneurs 

**Commande utilisée :**
```bash
docker run -d -p 8081:80 --name web-eval nginx:alpine
```
>J'ai modifié le port car il était déjà utilisé sur ma machine.

### 2.2 Inspection du conteneur 

- Adresse IP : 72.17.0.2
- Status : running
- Date de création : 2026-02-09T09:38:55.04722812Z


### 2.3 Logs et processus 

**Commande utilisée :**

```bash
docker logs --tail 10 web-eval
docker top web-eval
```
>Les commandes ont fonctionné.

### 2.4 Exécution de commandes 

**Commande utilisée :**

```bash
docker exec -it web-eval sh
#Une fois dans le conteneur :
echo "Tasnime Yahiaoui" > /tmp/evaluation.txt
ls /tmp
cat /tmp/evaluation.txt
exit
```
>Le fichier a bien été crée et son contenu vérifié.

## Exercice 3 : Cycle de vie

### 3.1 Arrêt et redémarrage 

**Commandes utilisées**

```bash
docker stop web-eval
docker ps -a
docker start web-eval
docker exec -it web-eval sh
ls /tmp
cat /tmp/evaluation.txt
exit
```

**Question :** Le fichier existe-t-il toujours ? Pourquoi ?
```text
Oui le fichier existe toujours car le conteneur n'a pas été supprimé, seulement arrêté puis redémarré.
```

### 3.2 Création d'un conteneur Redis 

**Commandes utilisées :**

```bash
docker run -d --name cache-eval redis:7-alpine
docker exec -it cache-eval redis-cli
#Une fois dans le CLI redis :
SET evaluation "reussie"
GET evaluation
exit
```

>Les commandes ont bien été éxecuté.

### 3.3 Gestion multiple

**Questions :**

- Quelles commandes avez-vous utilisées ?
```bash
docker ps -a
docker stop $(docker ps -q)
docker container prune
```
- Quelle est la différence entre `docker stop` et `docker rm` ?
```text
docker stop arrête un conteneur en cours d'exécution sans le supprimer, tandis que docker rm supprime définitivement le conteneur, ainsi que son système de fichiers.
```

## Exercice 4 : Volumes et persistance

### 4.1 Création d'un volume 

**Commande utilisée :**
```bash
docker run --rm -v data-eval:/data alpine sh -c "echo 'test volume docker' > /data/persistant.txt"
```

### 4.3 Vérification de la persistance 

**Commande utilisée :**
```bash
docker run --rm -v data-eval:/data alpine cat /data/persistant.txt
```

**Question :** Expliquez pourquoi les données persistent entre les conteneurs.
```text
Les données persistent car elles sont stockées dans un volume Docker, indépendant du conteneur.
Ainsi, même si le conteneur est supprimé, le volume conserve les données.
```

## Nettoyage

**Commandes utilisées**

```bash
docker ps -a #juste une vérification car les conteneurs ont été supprimé précédemment
docker volume rm data-eval
docker volume ls
```