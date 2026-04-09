FROM python:alpine

WORKDIR /app

RUN pip install redis requests

COPY worker.py .

CMD ["python", "worker.py"]
