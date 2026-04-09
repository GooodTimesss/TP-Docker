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
