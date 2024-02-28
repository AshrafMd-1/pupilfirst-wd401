# **GitHub Actions Pipeline:**

```yaml
name: CI & CD Integration
on: push
env:
  PG_DATABASE: ${{secrets.PG_DATABASE}}
  PG_USER: ${{secrets.PG_USER}}
  PG_PASSWORD: ${{secrets.PG_PASSWORD}}
jobs:
  run-tests:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: ${{secrets.PG_USER}}
          POSTGRES_PASSWORD: ${{secrets.PG_PASSWORD}}
          POSTGRES_DB: ${{secrets.PG_DATABASE}}
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - name: Check out repository code
        uses: actions/checkout@v3

      - name: Install dependencies
        run: cd todo-app && npm ci

      - name: Run unit tests
        run: cd todo-app && npm test

      - name: Run the app
        id: run-app
        run: |
          cd todo-app
          npm install
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm run start:dev &
          sleep 5

      - name: Run integration tests
        run: |
          cd todo-app
          npm install cypress cypress-json-results
          npx cypress run

      - name: Notify on success
        if: success()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"✨ Great News! The CI & CD workflow completed successfully! All tests passed smoothly. :tada:"}' ${{ secrets.SLACK_WEBHOOK_URL }}

      - name: Notify on failure
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"⚠️ Uh-oh! The CI & CD workflow encountered issues and failed. Immediate attention is needed. :warning:"}' ${{ secrets.SLACK_WEBHOOK_URL }}
```

- **Workflow Definition:**
    - Name: CI & CD Integration
    - Trigger: On push event
    - Environment Variables: PostgreSQL database connection parameters (PG_DATABASE, PG_USER, PG_PASSWORD)
- **Job Definition:**
    - Name: run-tests
    - Runs on: Ubuntu latest
- **PostgreSQL Service Setup:**
    - Service Name: postgres
    - Docker Image: postgres:11.7
    - Environment Variables for PostgreSQL: POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB
    - Health Check Options: pg_isready, health interval, health timeout, health retries
    - Exposed Ports: 5432 (mapped to 5432)
- **Steps:**
    - **Step 1: Check out Repository Code**
        - Uses: actions/checkout@v3
    - **Step 2: Install Dependencies and Run Unit Tests**
        - Install dependencies for todo-app
        - Run unit tests for todo-app
    - **Step 3: Set Up Database and Start the App**
        - Install dependencies for todo-app
        - Drop and recreate the database
        - Run database migrations
        - Start the application on port 3000
        - Wait for 5 seconds
    - **Step 4: Run Integration Tests**
        - Install Cypress and related dependencies
        - Run Cypress integration tests for todo-app
    - **Step 5: Notify on Success or Failure**
        - Notify Slack on workflow success with a success message
        - Notify Slack on workflow failure with a failure message
        - Uses a Slack webhook URL stored as an environment variable

# Automated Test Cases

## Unit Tests

- These are tests that are done to test the functionality of an app
- Using Jest for unit testing
<img width="325" alt="Pasted image 20240227202234" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/b9397d69-1330-42ba-a74e-f1fe7efb9be5">

## Integration Tests
- These are tests that are done to test the components
- Using cypress for component testing
<img width="564" alt="Pasted image 20240227203043" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/2af669fe-2f51-471c-8963-dc26b1f6752b">

## Output
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/9919f23c-cca2-483c-9e0e-f4db671987ee)

# **Environment Variable Configuration:**
An environment variable is a variable outside of the application code that stores configuration settings or other information. It is a key-value pair that can be accessed by programs running on the system. In the context of GitHub Actions, environment variables are used to store sensitive information, configuration parameters, or any data that needs to be accessed by the workflow during its execution.

## To Set up Environment Variables in GitHub Actions
- Go to your GitHub repository.
- Click on "Settings" in the menu.
- In the left sidebar, click on "Secrets."
- Click on "New repository secret."
- Enter the name and value for your secret (environment variable), then click "Add secret."

## Currently Configured Secrets
The pipeline relies on specific environment variables crucial for the application's functionality across different stages. These variables include:

- `PG_DATABASE`: Name of the PostgreSQL database.
- `PG_USER`: Name of the PostgreSQL user.
- `PG_PASSWORD`: Password for the PostgreSQL user.
- `SLACK_WEBHOOK_URL`: Webhook URL for Slack.

To safeguard sensitive and environment-specific information, these environment variables are securely stored in the Action secrets and variables section within the repository settings.

In the action workflow file, these variables are accessed and utilized as follows:

```yaml
env:
  PG_DATABASE: ${{secrets.PG_DATABASE}}
  PG_USER: ${{secrets.PG_USER}}
  PG_PASSWORD: ${{secrets.PG_PASSWORD}}
```

```yaml
      - name: Notify on success
        if: success()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"✨ Great News! The CI & CD workflow completed successfully! All tests passed smoothly. :tada:"}' ${{ secrets.SLACK_WEBHOOK_URL }}

      - name: Notify on failure
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"⚠️ Uh-oh! The CI & CD workflow encountered issues and failed. Immediate attention is needed. :warning:"}' ${{ secrets.SLACK_WEBHOOK_URL }}
```

This configuration ensures that the necessary environment variables are appropriately set within the CI/CD pipeline, maintaining security and allowing seamless integration across different stages of the application development process.

# Error Reporting Integration
The CI/CD pipeline seamlessly incorporates error reporting, promptly alerting the development team to any issues encountered during execution. This process involves posting comprehensive error messages and logs to a specified Slack channel for swift attention.

To set up this error reporting integration with Slack, follow these steps:

1. **Slack App Creation:**
   - Initiate the process by creating a new Slack app via the Slack API website.
   - Navigate to the Slack API website, access the workspace, and follow the guided steps to create a dedicated Slack app.

2. **App Installation:**
   - Once the Slack app is created, install it into the desired workspace.
   - Obtain the webhook URL associated with the newly installed Slack app.

3. **Webhook URL Configuration:**
   - Integrate the obtained webhook URL into the action workflow file to facilitate seamless communication between the CI/CD pipeline and the designated Slack channel.

```yaml
on:
  push:
    branches:
      - main

jobs:
  error-reporting:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository code
        uses: actions/checkout@v3

      # Other steps for the CI/CD pipeline...

      - name: Notify on Failure
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' \
          --data '{"text":"❗ Pipeline Execution Error\nDetails: '$(cat error-log.txt)'"}' \
          ${{ secrets.SLACK_WEBHOOK_URL }}
```

   - In this example, the `SLACK_WEBHOOK_URL` secret contains the obtained webhook URL.

## Explanation
So during the process of the pipeline if there is any error it sends a message on slack by utilizing the webhook
- It posts a message to slack that there is an error and sends the error log

## Output

- on failure
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/1a638fee-96b5-4d21-8ea7-b5b07db627ed)
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/18187b7f-b7dc-4493-aea8-7fd68d498482)

- on success
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/f4d2e6b7-ad65-4fed-a251-95bcb7e5b3c8)
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/14105d40-6280-4b99-9b3c-262ffa9c58ed)

By following these steps, the pipeline establishes a direct communication channel with Slack, ensuring that the development team receives immediate and detailed notifications whenever errors occur during the execution of the CI/CD pipeline. This proactive approach enhances collaboration and facilitates rapid issue resolution.

# Video for the Project
