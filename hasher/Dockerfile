FROM ruby:alpine

WORKDIR /app

RUN gem install sinatra rackup puma

COPY hasher.rb .

EXPOSE 80

CMD ["ruby", "hasher.rb"]
