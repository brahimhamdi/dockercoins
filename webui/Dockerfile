FROM node:18-alpine AS builder
WORKDIR /app
COPY files/package.json .
RUN npm install --production

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY files/ .
RUN adduser -D nodejs && chown -R nodejs:nodejs /app
USER nodejs
EXPOSE 80
CMD ["node", "webui.js"]
