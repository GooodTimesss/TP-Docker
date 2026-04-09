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
