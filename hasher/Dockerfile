FROM ruby:alpine

WORKDIR /app

RUN apk add --no-cache build-base && gem install sinatra rackup puma

COPY hasher.rb .

EXPOSE 80

CMD ["ruby", "hasher.rb"]
