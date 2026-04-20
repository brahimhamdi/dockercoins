FROM node:18-alpine

WORKDIR /app

# Copie des fichiers statiques et du serveur
COPY files/ ./files/
COPY webui.js .

# Création d’un utilisateur non‑root
RUN adduser -D nodeuser && chown -R nodeuser:nodeuser /app

USER nodeuser

EXPOSE 80

CMD ["node", "webui.js"]
