FROM node:18-alpine

WORKDIR /app

# Installer express globalement (ou localement)
RUN npm install express

# Copier les fichiers
COPY files/ ./files/
COPY webui.js .

RUN adduser -D nodeuser && chown -R nodeuser:nodeuser /app
USER nodeuser

EXPOSE 80
CMD ["node", "webui.js"]
