# Abstract HR Platform

Платформа автоматизации HR процессов для работы с вакансиями и заявками кандидатов.

## Запуск проекта в облачной среде

### Предварительные требования

- Docker и Docker Compose
- SSL сертификаты для вашего домена
- Доступ к облачному серверу с открытыми портами 80 и 443
- Telegram бот (опционально для отправки уведомлений)
- Доступ к API HeadHunter (опционально для интеграции с HeadHunter)

### Шаги для развертывания в продакшн

1. **Клонирование репозитория**

```bash
git clone https://github.com/yourusername/abstract-hr-platform.git
cd abstract-hr-platform
```

2. **Настройка переменных окружения**

Скопируйте пример файла `.env.example` в `.env` и заполните необходимые переменные:

```bash
cp .env.example .env
```

Отредактируйте файл `.env`, указав следующие параметры:
- Укажите реальный SECRET_KEY для безопасности
- Установите правильные учетные данные для базы данных
- Укажите настоящий домен в CORS_ORIGINS
- Добавьте токен вашего Telegram бота (если используется)
- Добавьте API ключи HeadHunter (если используется интеграция)

3. **Настройка Nginx**

Отредактируйте файл `nginx.conf`, заменив `your-domain.com` на ваш реальный домен.

4. **Подготовка SSL сертификатов**

Создайте директорию для SSL сертификатов:

```bash
mkdir -p ssl
```

Скопируйте ваши SSL сертификаты в директорию `ssl/`:
- `ssl/cert.pem` - сертификат
- `ssl/key.pem` - приватный ключ

Либо используйте Let's Encrypt для получения бесплатных сертификатов:

```bash
# Установка certbot
apt update
apt install certbot

# Получение сертификатов
certbot certonly --standalone -d your-domain.com

# Копирование сертификатов
cp /etc/letsencrypt/live/your-domain.com/fullchain.pem ssl/cert.pem
cp /etc/letsencrypt/live/your-domain.com/privkey.pem ssl/key.pem
```

5. **Запуск проекта в продакшене**

```bash
docker-compose -f docker-compose.prod.yml up -d
```

6. **Проверка запуска контейнеров**

```bash
docker-compose -f docker-compose.prod.yml ps
```

7. **Настройка автоматического обновления SSL сертификатов**

Если вы используете Let's Encrypt, добавьте задачу в crontab для обновления сертификатов:

```bash
crontab -e
```

Добавьте следующую строку:

```
0 3 * * * certbot renew --post-hook "cp /etc/letsencrypt/live/your-domain.com/fullchain.pem /path/to/abstract-hr-platform/ssl/cert.pem && cp /etc/letsencrypt/live/your-domain.com/privkey.pem /path/to/abstract-hr-platform/ssl/key.pem && docker restart abstract_hr_nginx"
```

## Запуск проекта в режиме разработки

Для локальной разработки можно использовать обычный docker-compose:

```bash
docker-compose up -d
```

## Обновление проекта

Для обновления проекта до последней версии:

```bash
git pull
docker-compose -f docker-compose.prod.yml down
docker-compose -f docker-compose.prod.yml build
docker-compose -f docker-compose.prod.yml up -d
```

## Структура проекта

- `backend/` - FastAPI бэкенд
- `frontend/` - Next.js фронтенд
- `docker-compose.yml` - конфигурация для разработки
- `docker-compose.prod.yml` - конфигурация для продакшена
- `Dockerfile.backend` - Docker-конфигурация для бэкенда
- `Dockerfile.frontend` - Docker-конфигурация для фронтенда
- `nginx.conf` - конфигурация Nginx для продакшена

## Резервное копирование данных

Для создания резервной копии базы данных:

```bash
docker exec abstract_hr_postgres pg_dump -U postgres abstract_hr > backup_$(date +%Y%m%d).sql
```

Для восстановления базы данных из резервной копии:

```bash
cat backup_file.sql | docker exec -i abstract_hr_postgres psql -U postgres abstract_hr
```

## Project Structure

- `frontend/` - Next.js frontend application
- `backend/` - FastAPI backend application

## Setup Instructions

### Backend Setup
1. Navigate to backend directory:
   ```bash
   cd backend
   ```
2. Create and activate virtual environment:
   ```bash
   python -m venv venv
   .\venv\Scripts\activate  # Windows
   source venv/bin/activate  # Linux/Mac
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Run the backend server:
   ```bash
   uvicorn main:app --reload
   ```
   The API will be available at http://localhost:8000

### Frontend Setup
1. Navigate to frontend directory:
   ```bash
   cd frontend
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run the development server:
   ```bash
   npm run dev
   ```
   The application will be available at http://localhost:3000

## Features (Planned)
- Employee Management
- Recruitment and Hiring
- Time and Attendance
- Performance Management
- Training and Development
- Document Management
- Analytics and Reporting 