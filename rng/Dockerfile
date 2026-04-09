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
