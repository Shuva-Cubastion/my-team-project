# ---------- Stage 1: install dependencies ----------
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# ---------- Stage 2: slim runtime image ----------
FROM node:20-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY package*.json ./
RUN npm install --omit=dev
COPY --from=builder /app/*.js ./
EXPOSE 3001
USER node
CMD ["node", "index.js"]
