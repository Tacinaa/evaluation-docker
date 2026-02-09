# TP4 : Orchestration Avancée et Optimisation

## Exercice 1 : Sécurité et Secrets

### 1.1 Docker Secrets avec Compose

**Commandes utilisées :**

```shell
mkdir secrets -Force
"evaluser" | Out-File -Encoding ascii secrets/db_user.txt
"SuperSecretP@ss2024!" | Out-File -Encoding ascii secrets/db_password.txt
"evaldb" | Out-File -Encoding ascii secrets/db_name.txt

Get-Content secrets/db_user.txt
Get-Content secrets/db_password.txt
Get-Content secrets/db_name.txt

docker rm -f $(docker ps -aq)
docker compose up -d
docker compose ps
```

### 1.2 Scan de vulnérabilités 

**Commandes utilisées :**

```bash
docker images
docker run --rm aquasec/trivy image eval-app:v1 
```