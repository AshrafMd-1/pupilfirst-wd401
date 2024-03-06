# Dockerize the Application
## What is a Docker

Docker is a containerization platform that enables developers to package applications and their dependencies into isolated containers. These containers can be deployed consistently across various environments, ensuring that the application runs consistently regardless of the underlying system.

### Here Are Key Aspects of Docker

**1. Containerization**
**2. Docker Image**
**3. Dockerfile**
**4. Docker Engine**
**5. Container Orchestration**
**6. Portability and Isolation**
**7. Microservices Architecture**
**8. Continuous Integration and Deployment (CI/CD)**

## Docker File
- Create a file name `dockerfile`

```docker
# First stage to install dependencies  
FROM --platform=$BUILDPLATFORM node:lts as dependencies  
RUN apt-get update && apt-get install -y \  
    libgtk2.0-0 \  
    libgtk-3-0 \  
    libgbm-dev \  
    libnotify-dev \  
    libnss3 \  
    libxss1 \  
    libasound2 \  
    libxtst6 \  
    xauth \  
    xvfb  
  
# Second stage to set up the application  
FROM dependencies as base  
WORKDIR /app  
COPY package.json /  
EXPOSE 3000  
  
  
FROM base as production  
ENV NODE_ENV=production  
RUN npm install  
COPY . /app  
CMD node index.js  
  
FROM base as dev  
ENV NODE_ENV=development  
RUN npm install -g nodemon && npm install  
COPY . /app  
CMD npm run clean:start:dev
```

## Explanation

**1. First Stage (`dependencies`):**

```Dockerfile
FROM --platform=$BUILDPLATFORM node:lts as dependencies

RUN apt-get update && apt-get install -y \
    libgtk2.0-0 \
    libgtk-3-0 \
    libgbm-dev \
    libnotify-dev \
    libnss3 \
    libxss1 \
    libasound2 \
    libxtst6 \
    xauth \
    xvfb
```

This stage sets up the base image using the specified Node.js LTS version. It installs system dependencies required for running graphical applications within the container. This is particularly useful when dealing with headless browsers or GUI-based applications.

**2. Second Stage (`base`):**

```Dockerfile
FROM dependencies as base

WORKDIR /app
COPY package.json /
EXPOSE 3000
```

This stage builds on the previous `dependencies` stage. It sets the working directory to `/app`, copies the `package.json` file to the root directory, and exposes port 3000. This stage is a foundation for subsequent stages, defining the basic structure and dependencies for the application.

**3. Third Stage (`production`):**

```Dockerfile
FROM base as production

ENV NODE_ENV=production
RUN npm install
COPY . /app
CMD node index.js
```

In the `production` stage, the environment is set to production, dependencies are installed, and the entire application code is copied into the container. Finally, it sets the command to run the Node.js application using `node index.js`.

**4. Fourth Stage (`dev`):**

```Dockerfile
FROM base as dev

ENV NODE_ENV=development
RUN npm install -g nodemon && npm install
COPY . /app
CMD npm run clean:start:dev
```

Similar to the `production` stage, the `dev` stage sets the environment to development, installs `nodemon` globally, installs application dependencies, copies the application code, and sets the command to run the development server using `npm run clean:start:dev`.

# Configure Environment Variables in Docker

## What Are Environment Variables
Environment variables are dynamic values that can affect the behavior of software applications and operating systems. These variables are part of the environment in which a process runs, providing a way to customize and configure applications without modifying their code.

## Env File
- Create a file name `.env`

```env
PORT=3000  
DB_USER=postgres  
DB_PASSWORD=postgres  
DB_HOST=db  
  
# Development  
DEV_DATABASE=todo  
  
# Test  
TEST_DATABASE=todo  
  
# Production  
PROD_DATABASE=todo_prod
```

## Explanation

**1. `PORT=3000`:**
   - Specifies the port on which the application will listen for incoming connections. In this case, it is set to 3000.

**2. `DB_USER=postgres`:**
   - Sets the username for connecting to a database. In this example, it is set to "postgres," which is a common default for PostgreSQL databases.

**3. `DB_PASSWORD=postgres`:**
   - Specifies the password associated with the database user. Here, it is set to "postgres," which might be a default password for a PostgreSQL database.

**4. `DB_HOST=db`:**
   - Defines the hostname or IP address where the database server is running. In this case, it is set to "db," which could be a reference to another service within a containerized environment.

**5. `DEV_DATABASE=todo`:**
   - Sets the name of the development database. In this example, the development database is named "todo."

**6. `TEST_DATABASE=todo`:**
   - Specifies the name of the database used for testing purposes. In this case, the test database is also named "todo."

**7. `PROD_DATABASE=todo_prod`:**
   - Defines the name of the production database. In this example, the production database is named "todo_prod."

## Secure Practices

Securing sensitive information within a Docker environment is crucial to prevent unauthorized access and protect sensitive data. Here are several secure practices to manage sensitive information effectively:

**1. Use Environment Variables**
**2. Docker Secrets**
**3. Build Arguments for Build-Time Secrets**
**4. Multi-Stage Builds**
**5. Avoid Image Layers for Sensitive Data**
**6. Regularly Update Images and Dependencies**
**7. Secure Docker Daemon Access**
**8. Use Secure Registries**
**9. Monitor and Audit Docker Usage**

# **Define Docker Compose for Multiple Services**

## What is Docker Compose

Docker Compose is a tool for defining and managing multi-container Docker applications through a YAML configuration file. It allows developers to specify the services, networks, and volumes required for an application, simplifying the deployment and orchestration of complex, interconnected containerized applications.

**Key Points:**
1. *Configuration with YAML*
2. *Multi-Container Applications*
3. *Service Definitions*
4. *Modular Configuration*
5. *Simplified Deployment*
6. *Reusable Configuration*
7. *Environment Variables and Networking*
8. *Efficient Development Workflow*
9. *Compatibility with Docker Engine*
10. *Integration with Docker Swarm*

## Docker Compose File
- Create a file name `docker.compose.yml`

```docker
version: "3.8"  
  
services:  
  app:  
    build:  
      context: .  
      target: dev  
    image: todo-app:development  
    volumes:  
      - .:/app  
    ports:  
      - 3000:3000  
    env_file:  
      - .env  
    depends_on:  
      - db  
  
  db:  
    image: postgres:15  
    volumes:  
      - pg-dev-data:/var/lib/postgresql/data  
    env_file:  
      - .env  
    environment:  
      POSTGRES_USER: $DB_USER  
      POSTGRES_DB: $DEV_DATABASE  
      POSTGRES_PASSWORD: $DB_PASSWORD  
  
volumes:  
  pg-dev-data:
```

## Explanantion

**1. `version: "3.8"`:**
   - Specifies the version of the Docker Compose file format being used.

**2. `services:`:**
   - Defines the services that make up the application.

**3. `app:` Service:**
   - Configures the application service.

   - `build:`: Specifies the build context for the Dockerfile and sets the target stage to 'dev.'

   - `image:`: Sets the name of the Docker image to 'todo-app:development.'

   - `volumes:`: Mounts the current directory (`.`) to `/app` in the container.

   - `ports:`: Maps port 3000 on the host to port 3000 on the container.

   - `env_file:`: Loads environment variables from the `.env` file into the container.

   - `depends_on:`: Ensures the 'db' service starts before the 'app' service.

**4. `db:` Service:**
   - Configures the database service.

   - `image:`: Specifies the PostgreSQL image with version 15.

   - `volumes:`: Creates a named volume 'pg-dev-data' to persist PostgreSQL data.

   - `env_file:`: Loads environment variables from the `.env` file into the container.

   - `environment:`: Sets PostgreSQL-specific environment variables like user, database, and password.

**5. `volumes:`:**
   - Defines a named volume 'pg-dev-data' for persisting PostgreSQL data.

**Summary:**
This Docker Compose configuration defines two services ('app' and 'db') and their associated settings, dependencies, and volume configurations. It orchestrates the setup of a development environment for a Todo application with a Node.js service ('app') and a PostgreSQL database service ('db'). The use of named volumes ensures data persistence between container restarts. The configuration is designed to work seamlessly by loading environment variables from the `.env` file into the respective services.

## Output
- Run command `docker-compose up`

<img width="584" alt="image" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/eda6e5a6-692c-4381-bd3e-79a62ef8bf26">

<img width="593" alt="image" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/88dd3c87-5947-4816-b060-6567f3d2949b">

# Setup CICD Pipeline

## Introduction
A CI/CD Pipeline is a sequence of automated steps that facilitate the integration, testing, and deployment of software changes. It spans the entire software delivery lifecycle, from code commit to production release. The pipeline includes processes such as code compilation, unit testing, integration testing, artifact generation, deployment, and validation.

## Workflow File
- Create a file name `Docker.yml`

```yaml
name: Docker CI & CD  
  
on: [ push ]  
  
jobs:  
  run-tests:  
    runs-on: ubuntu-latest  
  
    steps:  
      - name: Checkout repository  
        uses: actions/checkout@v3  
  
      - name: Build Docker image  
        run: cd todo-app && docker-compose build  
  
      - name: Run Docker Compose  
        run: cd todo-app && docker-compose up -d & sleep 5  
  
      - name: Run unit tests  
        run: cd todo-app && docker-compose run app npm test  
  
      - name: Run integration tests  
        run: |  
          cd todo-app  
          docker-compose run app npx sequelize-cli db:drop  
          docker-compose run app npx sequelize-cli db:create  
          docker-compose run app npx sequelize-cli db:migrate  
          docker-compose run app npm run start:dev &  
          sleep 5  
          docker-compose run app npm install cypress cypress-json-results  
          docker-compose run app npx cypress run  
  
      - name: Notify on success  
        if: success()  
        run: |  
          curl -X POST -H 'Content-type: application/json' --data '{"text":"✨ Great News! The CI & CD workflow completed successfully! All tests passed smoothly. :tada:"}' ${{ secrets.SLACK_WEBHOOK_URL }}  
  
      - name: Notify on failure  
        if: failure()  
        run: |  
          curl -X POST -H 'Content-type: application/json' --data '{"text":"⚠️ Uh-oh! The CI & CD workflow encountered issues and failed. Immediate attention is needed. :warning:"}' ${{ secrets.SLACK_WEBHOOK_URL }}  
  
      - name: Stop and remove Docker containers  
        run: cd todo-app && docker-compose down
```

## Explanation
### Pipeline Explanation

1. **Workflow Triggering:**
   - The pipeline triggers on every push to the repository.

2. **Job Setup (`run-tests`):**
   - Configures a job named `run-tests` to be executed on an Ubuntu latest environment.

3. **Steps:**
   - **Checkout Repository:**
     - Uses GitHub Actions to checkout the repository at version 3.

   - **Build Docker image:**
     - Changes directory to `todo-app` and builds the Docker image using `docker-compose build`.

   - **Run Docker Compose:**
     - Starts Docker containers in detached mode, waits for 5 seconds to ensure they are up and running.

   - **Run Unit Tests:**
     - Executes unit tests using `npm test` within the 'app' Docker container.

   - **Run Integration Tests:**
     - Sets up the database, runs migrations, starts the application, and runs Cypress integration tests.

   - **Notify on Success/Failure:**
     - Sends a notification to a Slack webhook based on the success or failure of the previous steps.

   - **Stop and Remove Docker Containers:**
     - Stops and removes the Docker containers using `docker-compose down` to clean up after the tests.

### Documentation
   - **Pipeline Configuration:**
     - Utilizes GitHub Actions for CI/CD.
     - Incorporates stages for building, testing, notifying, and cleaning up.
   - **Error Handling:**
     - Proper error handling is implemented using conditional steps (`if: success()` and `if: failure()`).
     - Notifications are sent to Slack in case of success or failure.

## Output

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/7ccc5176-0687-4ff7-8894-a1c52b5b6526)

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/fcbdf110-e0dd-4b04-af6f-feb1a7aed767)

This CI/CD pipeline automates the build, testing, and notification processes, ensuring a smooth and efficient development workflow.
