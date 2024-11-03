FROM python:3.10

WORKDIR /app

COPY . /app

# Install all required library from requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

EXPOSE 5153

ENV DB_USERNAME=longqb
ENV DB_PASSWORD=mypassword
ENV DB_HOST=127.0.0.1
ENV DB_PORT=5433
ENV DB_NAME=longqb-db

CMD ["python", "app.py"]