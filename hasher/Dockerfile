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
