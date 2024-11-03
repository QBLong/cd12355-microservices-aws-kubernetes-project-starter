# Coworking Space Service Extension

The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

For this project, you are a DevOps engineer who will be collaborating with a team that is building an API for business analysts. The API provides business analysts basic analytics data on user activity in the service. The application they provide you functions as expected locally and you are expected to help build a pipeline to deploy it in Kubernetes.

## Getting Started

### Dependencies

#### Local Environment

1. Python Environment - run Python 3.6+ applications and install Python dependencies via `pip`
2. Docker CLI - build and run Docker images locally
3. `kubectl` - run commands against a Kubernetes cluster
4. Postgres - install postgres and pgAdmin to run postgres command

#### Remote Resources

1. AWS CodeBuild - build Docker images remotely
2. AWS ECR - host Docker images
3. Kubernetes Environment with AWS EKS - run applications in k8s
4. AWS CloudWatch - monitor activity and logs in EKS
5. GitHub - pull and clone code

### Setup

#### 1. Configure a Database

1. Install Postgres and pgAdmin

Install Postgres and pgAdmin from the internet.

2. Configure database for the service

Run the following command to setup deployment for our postgres database

```bash
    kubectl apply -f pvc.yaml
    kubectl apply -f pv.yaml
    kubectl apply -f postgresql-deployment.yaml
    kubectl apply -f postgresql-service.yaml
```

Remember to forward port before seeding data

```bash
    kubectl port-forward --namespace default svc/<SERVICE_NAME>-postgresql 5432:5432 &
```

After that, we will seed the data to the database. We will run all the file in the db folder and change myuser and mydatabase to our username and database's name

```bash
    PGPASSWORD="$DB_PASSWORD" psql --host 127.0.0.1 -U <myuser> -d <mydatabase> -p 5433 < <FILE_NAME.sql>
```

### 2. Running the Analytics Application Locally

In the `analytics/` directory:

1. Install dependencies

```bash
pip install -r requirements.txt
```

2. Run the application (see below regarding environment variables)

```bash
<ENV_VARS> python app.py
```

There are multiple ways to set environment variables in a command. They can be set per session by running `export KEY=VAL` in the command line or they can be prepended into your command.

- `DB_USERNAME`
- `DB_PASSWORD`
- `DB_HOST` (defaults to `127.0.0.1`)
- `DB_PORT` (defaults to `5432`)
- `DB_NAME` (defaults to `-db`)

If we set the environment variables by prepending them, it would look like the following:

```bash
DB_USERNAME=username_here DB_PASSWORD=password_here python app.py
```

The benefit here is that it's explicitly set. However, note that the `DB_PASSWORD` value is now recorded in the session's history in plaintext. There are several ways to work around this including setting environment variables in a file and sourcing them in a terminal session.

3. Verifying The Application

- Generate report for check-ins grouped by dates
  `curl <BASE_URL>/api/reports/daily_usage`

- Generate report for check-ins grouped by users
  `curl <BASE_URL>/api/reports/user_visits`

### 3. Deploy our application

Apply all command below

```bash
cd deployment
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f coworking.yaml
```

### 4. Troubleshooting

1. Fail to run docker locally
   In this case, you should check if your docker desktop is at latest version and whether you have enable host networking or not. If you haven't, enable it and then run again.

2. Error while deploying
   You can run docker logs <pods> inorder to see why it fail and then solve the cause that make your deployment fails
