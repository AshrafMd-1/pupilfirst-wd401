# Testing Configuration

## What is Jest

jest is a JavaScript testing framework developed by Facebook. It is widely used for testing JavaScript code, including unit testing, integration testing, and end-to-end testing for web applications. Jest is particularly known for its simplicity, speed, and ease of use.

## What is Cypress

### Introduction
Cypress is an end-to-end testing framework designed for modern web applications. It is used to write and run tests that simulate user interactions within a web browser. Cypress is known for its simplicity, speed, and ability to provide real-time feedback during the development and testing process.

### Configuration

```js
const { defineConfig } = require("cypress");

module.exports = defineConfig({
  e2e: {
    baseUrl: "http://localhost:3000",
    retries: 2,
    requestTimeout: 15000,
  },
});
```

In my Node.js project, I implemented a testing framework using Jest for unit tests and Cypress for integration tests.
## Test Suite

My test suite covers the to-do list application.
- Unit tests focus on individual functions, such as adding and updating to-dos.
- Integration tests focus on the working of the components, including database operations and UI functionality.

### Unit Tests

For example, we have unit tests to check the addition of a new todo item:

> Link to the actual code [Unit Tests (github.com)](https://github.com/AshrafMd-1/pupilfirst-wd201/blob/master/todo-app/__tests__/todos.js)

#### Sample Code

```javascript
test("Create a Todo-Item as user 1", async () => {
    const agent = request.agent(server);
    await login(agent, "tonystark@test.com", "password");
    const res = await agent.get("/todos");
    const csrfToken = extractCSRFToken(res);
    const response = await agent.post("/todos").send({
      title: "Buy milk",
      dueDate: new Date().toISOString(),
      completed: false,
      _csrf: csrfToken,
    });
    expect(response.statusCode).toBe(302);
    expect(extractTodoItems(await agent.get("/todos"))).toBe(1);
  });
```

#### Output
<img width="325" alt="Pasted image 20240227202234" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/b9397d69-1330-42ba-a74e-f1fe7efb9be5">

### Integration Tests

An integration test checks that adding a todo item works:

> Link to the actual code [Integration Tests (github.com)](https://github.com/AshrafMd-1/pupilfirst-wd201/tree/master/todo-app/cypress/e2e)
#### Files
<img width="186" alt="Pasted image 20240227202322" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/9fd7a2e6-10c4-4aad-a230-f443731a9ab5">

#### Sample Code

```javascript
it("Todo form should accept correct details", () => {  
  cy.get("#todo").type("Todo 2");  
  cy.get("#date").type("2021-12-14");  
  cy.get("button").contains("Add").click();  
  cy.get("#notify-success").then(($el) => {  
    expect($el.text().trim()).to.equal("Todo added successfully");  
  });  
});
```

#### Output
<img width="564" alt="Pasted image 20240227203043" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/2af669fe-2f51-471c-8963-dc26b1f6752b">

## Automatic Test Suite Execution

I have set up GitHub Actions to run my test suite automatically on each push to the repository. The workflow configuration file (`.github/workflows/main.yml`) triggers the workflow on a `push` event and it involves running tests for a Node.js application with a PostgreSQL database"

### GitHub Workflow File

```yaml
name: Auto Test Todo App
on: push
env:
  PG_DATABASE: todo_db_test
  PG_USER: postgres
  PG_PASSWORD: ashraf
jobs:
  run-tests:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: ashraf
          POSTGRES_DB: todo_db_test
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
          curl -X POST -H 'Content-type: application/json' --data '{"text":"The Auto Test Todo App workflow has passed successfully! :tada:"}' ${{secrets.SLACK_WEBHOOK_URL}}

      - name: Notify on failure
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"The Auto Test Todo App workflow has failed. :warning:"}' ${{secrets.SLACK_WEBHOOK_URL}}
```

### Part 1: Define Workflow

```yaml
name: Auto Test Todo App
on: push
env:
  PG_DATABASE: todo_db_test
  PG_USER: postgres
  PG_PASSWORD: ashraf
```

This part defines the workflow name, triggers it on a push event, and sets up environment variables for PostgreSQL database connection parameters.

### Part 2: Define Jobs

```yaml
jobs:
  run-tests:
    runs-on: ubuntu-latest
```

Here, a job named `run-tests` is defined, specifying that it will run on an Ubuntu environment.

### Part 3: Set Up PostgreSQL Service

```yaml
    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: ashraf
          POSTGRES_DB: todo_db_test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
```

This section sets up a PostgreSQL service using the official PostgreSQL Docker image. It configures the necessary environment variables, health check options, and exposes the PostgreSQL port.

### Part 4: Define Steps

```yaml
    steps:
      - name: Check out repository code
        uses: actions/checkout@v3
```

The workflow starts by checking out the code repository using the `actions/checkout` action.

### Part 5: Install Dependencies and Run Unit Tests

```yaml
      - name: Install dependencies
        run: cd todo-app && npm ci

      - name: Run unit tests
        run: cd todo-app && npm test
```

This part installs Node.js dependencies and runs unit tests for the `todo-app`.

### Part 6: Set Up Database and Start the App

```yaml
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
```

This step sets up the database for the application by dropping and recreating it. It also runs database migrations and starts the application on port 3000.

### Part 7: Run Integration Tests

```yaml
      - name: Run integration tests
        run: |
          cd todo-app
          npm install cypress cypress-json-results
          npx cypress run
```

This step installs Cypress and runs integration tests against the running application.

### Part 8: Connect To Slack

```yaml
      - name: Notify on success
        if: success()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"The Auto Test Todo App workflow has passed successfully! :tada:"}' ${{secrets.SLACK_WEBHOOK_URL}}

      - name: Notify on failure
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' --data '{"text":"The Auto Test Todo App workflow has failed. :warning:"}' ${{secrets.SLACK_WEBHOOK_URL}}
```

Finally, this step will notify your Slack channel about the success or failure of the integration tests.

> ${{secrets.SLACK_WEBHOOK_URL}} is a environment variable

This GitHub Actions workflow ensures that the application is set up, dependencies are installed, and both unit and integration tests are executed whenever there's a push event to the repository.

### Output

#### Unit Tests
![Pasted image 20240227205508](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/6ce43236-5891-4bec-9469-a3538c302937)

#### Integration Tests
![Pasted image 20240227205656](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/3b1e52d8-f025-4f5c-af36-a81256fc52fa)

#### Slack Message
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/26655e47-76e7-4e7d-989b-cc96db0f771c)

This is how testing and GitHub workflows are set up.
