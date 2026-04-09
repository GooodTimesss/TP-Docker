# Rapport TP Docker

### Groupe

- SEIDOU Ahmal Seidou
- SYLLA Sadio

---

## Partie 1 : Dockerfiles

### hasher/Dockerfile

```dockerfile
# Utilise l'image officielle Ruby sur Alpine Linux (légère)
FROM ruby:alpine

# Définit le répertoire de travail dans le conteneur
WORKDIR /app

# Installe les outils de compilation nécessaires pour les gems natifs,
# puis installe les gems Ruby : sinatra (framework web), rackup et puma (serveur HTTP)
RUN apk add --no-cache build-base && gem install sinatra rackup puma

# Copie le code source de l'application dans le conteneur
COPY hasher.rb .

# Expose le port 80 sur lequel l'application écoute
EXPOSE 80

# Lance l'application hasher au démarrage du conteneur
CMD ["ruby", "hasher.rb"]
```

---

### rng/Dockerfile

```dockerfile
# Utilise l'image officielle Python sur Alpine Linux (légère)
FROM python:alpine

# Définit le répertoire de travail dans le conteneur
WORKDIR /app

# Installe la librairie Flask (framework web Python)
RUN pip install flask

# Copie le code source de l'application dans le conteneur
COPY rng.py .

# Expose le port 80 sur lequel l'application écoute
EXPOSE 80

# Lance le générateur de nombres aléatoires au démarrage du conteneur
CMD ["python", "rng.py"]
```

---

### worker/Dockerfile

```dockerfile
# Utilise l'image officielle Python sur Alpine Linux (légère)
FROM python:alpine

# Définit le répertoire de travail dans le conteneur
WORKDIR /app

# Installe les librairies Python nécessaires :
# redis (communication avec la base de données Redis)
# requests (appels HTTP vers rng et hasher)
RUN pip install redis requests

# Copie le code source de l'application dans le conteneur
COPY worker.py .

# Lance le worker au démarrage du conteneur (pas de port exposé car pas de serveur HTTP)
CMD ["python", "worker.py"]
```

---

### webui/Dockerfile

```dockerfile
# Utilise l'image officielle Node.js sur Alpine Linux (légère)
FROM node:alpine

# Définit le répertoire de travail dans le conteneur
WORKDIR /app

# Installe les dépendances Node.js :
# express (framework web), redis@3 (client Redis version 3)
RUN npm install express redis@3

# Copie tous les fichiers sources dans le conteneur (webui.js + dossier files/)
COPY . .

# Expose le port 80 sur lequel l'interface web écoute
EXPOSE 80

# Lance le serveur web de l'interface utilisateur au démarrage du conteneur
CMD ["node", "webui.js"]
```

---

## Partie 2 : docker-compose.yml

```yaml
services:
  # Base de données Redis : stocke les hashes trouvés et la vitesse de minage
  redis:
    image: redis

  # Service rng : génère des octets aléatoires (source d'entropie pour le minage)
  rng:
    build: rng

  # Service hasher : calcule le hash SHA256 des octets reçus
  hasher:
    build: hasher

  # Service worker : orchestre le minage (appelle rng, hasher et stocke dans redis)
  worker:
    build: worker

  # Interface web : affiche les statistiques de minage en temps réel
  # Exposée sur le port 8000 de la machine hôte
  webui:
    build: webui
    ports:
      - "8000:80"
```

---

## Bonus : Analyse des performances et optimisation

### Performances de base (1 worker)

Avec 1 seul worker, la vitesse de minage est d'environ **~5 hashes/seconde**.

Chaque cycle de travail du worker effectue :
1. `time.sleep(0.1)` dans `worker.py`
2. Appel HTTP à `rng` qui effectue lui aussi `time.sleep(0.1)`
3. Appel HTTP à `hasher`

Soit un minimum de 0.2s par hash, ce qui donne ~5 hashes/sec.

### Scaling à 10 workers (sans fix)

```bash
docker compose up -d --scale worker=10
```

Résultat : **~45 hashes/seconde** — quasi identique à la base.

**Explication :** Le service `rng` est le bottleneck. Avec un seul container `rng`,
tous les workers se retrouvent à attendre leur tour pour obtenir des octets aléatoires.
Même avec `threaded=True` dans Flask, le `time.sleep(0.1)` limite le débit total.

### Fix : scaler rng en même temps que worker

```bash
docker compose up -d --scale worker=10 --scale rng=10
```

Résultat : **~50 hashes/seconde** — soit **~10x la base**.

**Explication :** Avec 10 instances de `rng`, chaque worker dispose de sa propre
source de nombres aléatoires sans contention. Le bottleneck est supprimé et les
performances scalent linéairement avec le nombre de workers.

### Résumé

| Configuration            | Performances       |
|--------------------------|--------------------|
| 1 worker, 1 rng          | ~5 hashes/sec      |
| 10 workers, 1 rng        | ~45 hashes/sec     |
| 10 workers, 10 rng       | ~50 hashes/sec     |
