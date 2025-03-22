# Github-Actions-and-CI-CD-Project-YAML

### Workflow syntax and structure

**Objectives**
- Understand YAML syntax for workflows
- Learn the structure and components of a workflow.

1.YAML syntax for Workflow

- Yaml is a human-readable data serialization standard used for configuration files.
- Key concepts-Indentation,key-value pair,list.

Example snippet.

    name: CI Workflow

    on:
    push:
        branches:
            - main



2.Workflow Structure and Components.

- Workflow File: Located in ` .github/workflow ` directory .Eg `main.yml`
- Jobs : Define task like building,testing and deploying
- Steps : Individual task within a job
- Actions : Reusable units of code within steps
- Events : Triggers for the workflow eg push,pull_request.
- Runners : The servers where the job runs eg ubuntu-latest.


### Implementing Continuous Integration.

Lesson 1: Building and Testing Codes

Objectives:
- Set up build step in github actions
- Run test as part of the CI process.

**Setting Up Build Steps**

- In your Github workflow file (eg` .github/workfow/main.yml`),start be defining a `job` named `build`.

- This job is responsible for building your code.

        jobs:
          build:
            runs-on: ubuntu-latest
            steps:
            # Steps will be defined next

**Adding Build Steps**

- Each step in the job performs a specific task.
- Here, we added three steps: Checking out the code, installing dependencies, and running the build script.



```yaml
steps:
  - uses: actions/checkout@v2
    # 'actions/checkout@v2' is a pre-made action that checks out your repository under $GITHUB_WORKSPACE, so your workflow can access it.

  - name: Install dependencies
    run: npm install
    # 'npm install' installs the dependencies defined in your project's 'package.json' file.

  - name: Build
    run: npm run build
    # 'npm run build' runs the build script defined in your 'package.json'. This is typically used for compiling or preparing your code for deployment.
```

**Running Test In The Workflow**

1.Adding Test steps
 - After your build steps,include steps to execute your test scripts.
 - This ensures that your code is not only built,but also passes all the test.


```yaml
steps:
  - uses: actions/checkout@v2
    # 'actions/checkout@v2' is a pre-made action that checks out your repository under $GITHUB_WORKSPACE, so your workflow can access it.

  - name: Install dependencies
    run: npm install
    # 'npm install' installs the dependencies defined in your project's 'package.json' file.

  - name: Build
    run: npm run build
    # 'npm run build' runs the build script defined in your 'package.json'. This is typically used for compiling or preparing your code for deployment.

  - name: Run tests
    run: npm test
    # 'npm test' runs the test script defined in your 'package.json'. It's crucial for ensuring that your code works as expected before deployment.
```

#### Additional YAML Concepts In Github ACtions:

Detailed Steps and Code Explanation.

1.Using Environmental variables.

- Environment variables can be defined at the workflow,job or step level.
- They allow you to dynamically pass configurations and settings.

```yaml
env:
  CUSTOM_VAR: value
  # Define an environment variable 'CUSTOM_VAR' at the workflow level.

jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - name: Use environment variable
        run: echo $CUSTOM_VAR
        # Access 'CUSTOM_VAR' in a step.
```

2.Working with secrets

- Secrets are encrypted set in your GITHUB repository settings.
- Ideal for storing sensitive data like access Tokens,passwords,etc.



```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Use secret
        run: |
          echo "Access Token: ${{ secrets.ACCESS_TOKEN }}"
          # Use 'ACCESS_TOKEN' secret defined in the repository settings.
```

3.Conditional Execution

You can control when jobs, steps or workflow run based on conditions.


```yaml
jobs:
  conditional-job:
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    # The job runs only for push events to the 'main' branch.
    steps:
      - uses: actions/checkout@v2
```

3.Using Output and Input between steps

Share data between steps in a job using output.



```yaml
jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - id: step-one
        run: echo "::set-output name=value::$(echo hello)"
        # Set an output named 'value' in 'step-one'.
      
      - id: step-two
        run: |
          echo "Received value from previous step: ${{ steps.step-one.outputs.value }}"
          # Access the output of 'step-one' in 'step-two'.
```
4.Configure Build Matrices

A **build matrix** allows you to test your application across a combination of operating systems, language versions, or any other variables.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [12, 14, 16] # Test across Node.js versions
        os: [ubuntu-latest, windows-latest, macos-latest] # Test across OS
    steps:
      - uses: actions/checkout@v3
        # Checkout your code

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
        # Set up the Node.js version defined in the matrix

      - name: Install dependencies
        run: npm install
        # Install project dependencies

      - name: Run tests
        run: npm test
        # Run tests across all matrix combinations
```

This matrix setup will run tests across multiple Node.js versions (`12`, `14`, `16`) and operating systems (`Ubuntu`, `Windows`, and `macOS`).

5.Implement Parallel and Matrix Build

Parallelization is automatically handled when you use matrix strategies—each combination defined in the matrix runs in parallel, significantly reducing build times.


```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build and Test
        run: echo "Running build and tests"

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to staging
        run: echo "Deploying to staging environment"
```


6.Manage Dependencies Across Different Environments
To manage environment-specific dependencies, you can use conditionals or environment variables:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        env: [development, staging, production] # Define environments
    steps:
      - name: Install dependencies
        run: |
          if [ "${{ matrix.env }}" == "production" ]; then
            npm install --only=prod
          else
            npm install
          fi
        # Install production-only dependencies for the 'production' environment
```

