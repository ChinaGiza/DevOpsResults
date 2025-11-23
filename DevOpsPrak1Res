# Личный кабинет в ИСУ

## Описание проекта
> Данный проект направлен на ==обеспечение эффективности== взаимодействия между пользователем (студент или преподаватель) во учебного/рабочего процесса

## Используемые технологии
* FrontEND:
	- [ ] React
  - [ ] Next.js
	- [ ] Redux Toolkit
* BackEND:
  - [ ] Node.js с Express.js
* Контейнеризация:
  - [ ] Docker
* База данных:
  - [ ] PostgreSQL
## Версии ключевых компонентов

|Компонент|Версия|
|-|-|
|React|v18.2.0|
|Next.js|v14.0.0|
|Redux Toolkit|v1.9.7|
|Node.js|v18.0.0|
|Express.js|v4.18.2|
|Docker|24.0.+|
|PostgreSQL|16.0.0|

## Dockerfile
Ссылка на местонахождение: [Dockerfile](Dockerfile)
``` Dockerfile
# Backend Dockerfile
FROM node:18-alpine

WORKDIR /app

# Копируем package.json и package-lock.json
COPY package*.json ./
COPY yarn.lock* ./

# Устанавливаем зависимости
RUN npm ci --only=production

# Копируем исходный код
COPY . .

# Создаем непривилегированного пользователя
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Меняем владельца файлов
RUN chown -R nextjs:nodejs /app
USER nextjs

# Открываем порт
EXPOSE 5000

# Запускаем приложение
CMD ["npm", "run", "dev"]
```

## Docker Compose
Ссылка на местонахождение: [Docker Compose](Docker Compose)
``` Docker Compose
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    container_name: app_postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-myapp}
      POSTGRES_USER: ${POSTGRES_USER:-user}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-password}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-user} -d ${POSTGRES_DB:-myapp}"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Backend API Service
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: app_backend
    environment:
      NODE_ENV: ${NODE_ENV:-development}
      DATABASE_URL: postgresql://${POSTGRES_USER:-user}:${POSTGRES_PASSWORD:-password}@postgres:5432/${POSTGRES_DB:-myapp}
      PORT: 5000
    ports:
      - "5000:5000"
    depends_on:
      postgres:
        condition: service_healthy
    volumes:
      - ./backend:/app
      - /app/node_modules
    networks:
      - app-network
    restart: unless-stopped

  # Frontend Next.js Service
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: app_frontend
    environment:
      NODE_ENV: ${NODE_ENV:-development}
      NEXT_PUBLIC_API_URL: http://localhost:5000/api
    ports:
      - "3000:3000"
    depends_on:
      - backend
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next
    networks:
      - app-network
    restart: unless-stopped

volumes:
  postgres_data:

networks:
  app-network:
    driver: bridge
```