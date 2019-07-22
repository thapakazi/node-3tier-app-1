FROM node:6.5

COPY ./bin /app/bin
COPY ./app.js /app/
COPY ./package.json /app/
WORKDIR /app
RUN npm install

EXPOSE 8080
ENV PORT=8080
ENV DB=postgres://api_user:apipassword@localhost/node-3tier-api

HEALTHCHECK --interval=10s --timeout=3s \
    CMD curl -f -s http://localhost:8080/api/status

ENTRYPOINT ["npm", "start"]

