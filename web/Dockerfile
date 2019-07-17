FROM node:6.5

COPY ./bin /app/bin
COPY ./public /app/public
COPY ./routes /app/routes
COPY ./views /app/views
COPY ./app.js /app/
COPY ./package.json /app/
WORKDIR /app
RUN npm install

EXPOSE 80
ENV PORT=80
ENV API_HOST=api.local


ENTRYPOINT ["npm", "start"]