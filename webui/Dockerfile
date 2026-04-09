FROM node:alpine

WORKDIR /app

RUN npm install express redis@3

COPY . .

EXPOSE 80

CMD ["node", "webui.js"]
