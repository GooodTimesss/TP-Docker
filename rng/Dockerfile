FROM python:alpine

WORKDIR /app

RUN pip install flask

COPY rng.py .

EXPOSE 80

CMD ["python", "rng.py"]
