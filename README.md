# Cars Dealership — Full-Stack Development Capstone Project

A responsive web application for **Cars Dealership**, a national car retailer in the U.S. Users can browse dealership branches across the country, filter them by state, read customer reviews (with sentiment analysis), register, log in, and post their own reviews.

## Features

- Browse all dealerships and filter them by state
- View a dealer's details and its customer reviews, each tagged positive / neutral / negative
- Sign up, log in and log out
- Logged-in users can post a review for any dealer
- Admins manage car makes and models through the Django admin site
- Static "About Us" and "Contact Us" pages

## Architecture

| Component | Technology | Port |
|---|---|---|
| Web app and user management | Django + SQLite, React frontend | 8000 |
| Dealers and reviews API | Node.js + Express + MongoDB | 3030 |
| Sentiment analyzer | Flask + NLTK VADER (IBM Code Engine) | 5000 |

Django serves the React build and talks to the Node API and the sentiment microservice through `server/djangoapp/restapis.py`.

## Project structure

```
server/
├── database/            Node.js + MongoDB dealers/reviews API (Docker Compose)
├── djangoapp/           Django app: models, views, urls, REST client
│   └── microservices/   Flask sentiment analyzer
├── djangoproj/          Django project settings
├── frontend/            React app + static pages (Home, About, Contact)
├── Dockerfile           Django + React image
└── deployment.yaml      Kubernetes deployment
.github/workflows/       CI: flake8 + JSHint lint
```

## Running locally

```bash
# 1. Dealers/reviews API + MongoDB
cd server/database
docker build . -t nodeapp
docker compose up -d

# 2. Sentiment analyzer
cd ../djangoapp/microservices
docker build . -t senti_analyzer
docker run -d -p 5050:5000 senti_analyzer

# 3. React frontend
cd ../../frontend
npm install && npm run build

# 4. Django
cd ..
pip install -r requirements.txt
python manage.py makemigrations && python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Set `backend_url` and `sentiment_analyzer_url` in `server/djangoapp/.env` to point at the API and the sentiment service.

## CI/CD

GitHub Actions (`.github/workflows/main.yml`) lints all Python files with flake8 and the Node.js files with JSHint on every push and pull request.

## Deployment

The Django app is containerized with `server/Dockerfile` and deployed to Kubernetes with `server/deployment.yaml`. The sentiment analyzer is deployed to IBM Cloud Code Engine.
