# Configuration

In my Node.js project, I implemented a testing framework using Jest for unit tests and Cypress for integration tests. The `package.json` file includes the necessary dependencies:

```json
"devDependencies": {
  "jest": "^27.0.6",
  "cypress": "^8.4.0",
  "cypress-json-results": "^1.3.0"
}
```

For Cypress, the configuration is in the `cypress.json` file, specifying the base URL for the application.

## Test Suite

My test suite covers the to-do list application. Unit tests focus on individual functions, such as adding and updating to-dos. Integration tests focus on the working of the components, including database operations and UI functionality.

### Unit Tests

For example, we have unit tests to check the addition of a new todo item:

```javascript
// __test__/todos.js
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

### Integration Tests

An integration test checks that adding a todo item works:

```javascript
// cypress/integration/todos.js

describe('Todo Tests', () => {
  it('Create a Todo-Item as user 1', () => {
    cy.visit('http://localhost:3000')
      .get('[data-cy=email]').type('tonystark@test.com')
      .get('[data-cy=password]').type('password')
      .get('[data-cy=loginButton]').click()
      .get('[data-cy=todosLink]').click()
      .get('[data-cy=title]').type('Buy milk')
      .get('[data-cy=dueDate]').type(new Date().toISOString())
      .get('[data-cy=completed]').uncheck()
      .get('[data-cy=createTodoButton]').click()
      .url().should('include', '/todos')
      .get('[data-cy=todoItem]').should('have.length', 1);
  });
});

```

## Automatic Test Suite Execution

I have set up GitHub Actions to run my test suite automatically on each push to the repository. The workflow configuration file (`.github/workflows/test.yml`) triggers the workflow on a `push` event:

```yaml
name: Automated Tests

on:
  push:
    branches:
      - main

jobs:
  run-tests:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: wd-todo-test
        ports:
          - 5432:5432

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Install dependencies
        run: cd l10 && npm ci

      - name: Run unit tests
        run: cd l10 && npm test

      - name: Setup and run app
        run: |
          cd l10
          npm install
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5

      - name: Run integration tests
        run: |
          cd l10
          npm install cypress cypress-json-results
          npx cypress run --env STUDENT_SUBMISSION_URL="http://localhost:3000/"
```

# GitHub Actions Walkthrough

## Workflow

My GitHub Actions workflow automatically runs the test suite for the Node.js project on every push to the `main` branch. The workflow consists of multiple steps that set up the 
1. testing environment
2. install dependencies
3. execute both unit and integration tests.

## Workflow Configuration File

The workflow configuration is defined in the `.github/workflows/test.yml` file. Let's break down each part:

```yaml
name: Automated Tests

on:
  push:
    branches:
      - main
```

- **Name:** Defines the workflow's name as "Automated Tests."
- **On:** Specifies the trigger for the workflow. In this case, it runs on every push to the `main` branch.

## Job Configuration

```yaml
jobs:
  run-tests:
    runs-on: ubuntu-latest
```

- **Job Name:** The job is named `run-tests`.
- **Runs On:** Specifies the OS for the job. In this case, it runs on the latest version of Ubuntu.

## Service Configuration

```yaml
    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: wd-todo-test
        ports:
          - 5432:5432
```

- **Services:** Configures a PostgreSQL service to create a database for testing.
  - **Image:** Specifies the Docker image for PostgreSQL version 11.7.
  - **Environment Variables:** Sets up necessary environment variables for the PostgreSQL service.
  - **Ports:** connects the PostgreSQL container's port 5432 to the host machine's port 5432.

## Steps Execution

```yaml
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
```

- **Checkout Code:** Utilizes the `actions/checkout` action to fetch the latest code from the repository.

```yaml
      - name: Install dependencies
        run: cd l10 && npm ci
```

- **Install Dependencies:** Changes to the `l10` directory and runs `npm ci` to install project dependencies using the exact versions specified in `package-lock.json`.

```yaml
      - name: Run unit tests
        run: cd l10 && npm test
```

- **Run Unit Tests:** Executes the unit tests located in the `l10` directory using the `npm test` command.

```yaml
      - name: Setup and run app
        run: |
          cd l10
          npm install
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5
```

- **Setup and Run App:** Sets up the application environment by installing dependencies and performing database operations using Sequelize CLI. The application is started on port 3000 in the background.

```yaml
      - name: Run integration tests
        run: |
          cd l10
          npm install cypress cypress-json-results
          npx cypress run --env STUDENT_SUBMISSION_URL="http://localhost:3000/"
```

- **Run Integration Tests:** Installs Cypress and its dependencies, then executes integration tests using Cypress. The `STUDENT_SUBMISSION_URL` environment variable is set to the local application URL.

## Detailed Explanation

1. **Trigger:**
   - The workflow is triggered on every push event targeting the `main` branch.

2. **Job Creation:**
   - The `run-tests` job is initialized, specifying it runs on an Ubuntu environment.

3. **Service Config:**
   - The PostgreSQL service is configured to mimic a database, ensuring seamless integration testing.

4. **Steps Execution:**
   - Code is checked, dependencies are installed, and unit tests are executed.
   - The application is set up, including database operations, and started on port 3000.
   - Integration tests are then run using Cypress, simulating interactions with the application.

This is how testing and GitHub workflows are set up.
