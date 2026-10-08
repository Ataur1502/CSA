FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

COPY backend/requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY backend/ ./backend/

RUN addgroup --system app && adduser --system --ingroup app app \
    && mkdir -p /data \
    && chown -R app:app /app /data

WORKDIR /app/backend
ENV DJANGO_DB_PATH=/data/db.sqlite3

USER app
EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/api/simulation/state/', timeout=3)" || exit 1

CMD ["sh", "-c", "python manage.py migrate --noinput && python manage.py seed_data && python manage.py collectstatic --noinput && exec gunicorn --bind 0.0.0.0:8000 --workers 1 --access-logfile - --error-logfile - casabackend.wsgi:application"]
