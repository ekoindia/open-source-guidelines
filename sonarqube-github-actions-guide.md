# SonarQube Integration with Unit Testing

We use **SonarQube** in a Dockerized setup.

### **Prerequisites**

* Docker installed (`docker -v`)

### **Quick Start (Standalone Container)**

Use this for local development or quick scanning.

```yaml
  docker pull sonarqube
  docker run -d --name sonarqube-lts \
    -p 9000:9000 \
    -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
    sonarqube
```

* Visit: [http://localhost:9000](http://localhost:9000)  
* Login:  
  * **Username:** `admin`  
  * **Password:** `admin`

The same can be done using a docker-compose.yml file, just follow the steps below:

1. Create a docker-compose.yml file with the following content:

```yaml

version: '3.8'
services:
  sonarqube:
    image: sonarqube
    container_name: sonarqube
    restart: always
    environment:
      SONARQUBE_JDBC_URL: jdbc:postgresql://your-sonar-url:5432/sonarqube
      SONARQUBE_JDBC_USERNAME: ${SONAR_JDBC_USER}
      SONARQUBE_JDBC_PASSWORD: ${SONAR_JDBC_PASS}
      SONAR_AUTH_GITHUB_CLIENT_ID: ${GITHUB_CLIENT_ID}
      SONAR_AUTH_GITHUB_CLIENT_SECRET: ${GITHUB_CLIENT_SECRET}
      SONAR_AUTH_GITHUB_ORGANIZATION: ${GITHUB_ORG}
      SONAR_AUTH_GITHUB_ENABLED: "true"
    ports:
      - "127.0.0.1:9000:9000"
    volumes:
      - ./data:/opt/sonarqube/data
      - ./extensions:/opt/sonarqube/extensions
  ```

**Run with:**

```yaml
  docker-compose up -d
```

### **Verifying Installation**

* Open your browser: `http://your-sonar-url`  
* Login: `admin / admin`  
* Go to `Administration > System > About`  
* You should see: **SonarQube**

---

* The project_name and project_token are generated whenever a new project is created on **SonarQube.**  
* The above code snippet will also be generated individually whenever a new project is created on **SonarQube.**

# SonarQube Integration with GitHub Actions

## Prerequisites

1. **SonarQube** is installed and accessible at: `https://your-sonar-url`.
2. A **SonarQube admin account** to create projects and tokens.
3. Your project is hosted on **GitHub**.
4. You have access to the **SonarQube server token** (global or project-specific).

---

## 1. Create a Project in SonarQube

1. Visit your SonarQube instance (`https://your-sonar-url`).
2. Go to **Projects** → **Create Project**.
3. Choose **Manually**, then:
   - **Project Key**: `your-project` (must match in code and UI)
   - **Display Name**: `Your Project`
4. After creation, you'll be prompted to generate a **token**.
   - **Use your global token** or **create a new one**.
   - Save this token securely.

---

## 2. Add `sonar-project.properties` to Your Repo

Create a file named `sonar-project.properties` in the **root of your repo**:

```
sonar.projectName=YourProject
sonar.sources=src
```

- Adjust `sonar.sources` if your source files are located elsewhere (e.g., `src/app`, `lib`, etc.).

---

## 3. Add Sonar Token to GitHub Secrets

1. Go to your GitHub repository → **Settings** → **Secrets and variables** → **Actions**.
2. Click **"New repository secret"**.
3. Add:
   - **Name**: `SONAR_TOKEN`
   - **Value**: Your SonarQube token (global or project-specific)

   - **Name**: `SONAR_HOST_URL`
   - **Value**: Your SonarQube url

   - **Name**: `SONAR_PROJECT_KEY`
   - **Value**: The Project Key that is present on the UI and should match with the one in Github Secrets. A separate secret shall be created for each repo.

---

## 4. Set Up GitHub Actions Workflow

Create the file: `.github/workflows/sonar.yml`

###### For JS Projects:

```yaml
name: SonarQube Scan

on:
  push:
    branches:
      - main
      - dev

jobs:
  sonarqube:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20' # or '18' depending on your project
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests with coverage
        run: npm test -- --coverage

      - name: SonarQube Scan
        uses: SonarSource/sonarqube-scan-action@v1.2
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
        with:
          args: >
            -Dsonar.projectKey=${{ secrets.SONAR_PROJECT_KEY }}
            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info

      - name: SonarQube Quality Gate
        uses: SonarSource/sonarqube-quality-gate-action@v1.1
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

###### For Java Projects:

```yaml
name: SonarQube Scan (Java)

on:
  push:
    branches:
      - main
      - dev

jobs:
  sonarqube:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17' # set depending on your project
          distribution: 'temurin'
          cache: maven

      - name: Build and run tests with coverage
        run: mvn clean verify

      - name: SonarQube Scan
        uses: SonarSource/sonarqube-scan-action@v1.2
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
        with:
          args: >
            -Dsonar.projectKey=${{ secrets.SONAR_PROJECT_KEY }}
            -Dsonar.java.binaries=target/classes
            -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml

      - name: SonarQube Quality Gate
        uses: SonarSource/sonarqube-quality-gate-action@v1.1
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

---

## 5. Validate the Setup

1. Push your code to `main` or `dev`.
2. Navigate to **GitHub Actions** in your repo to see the workflow run.
3. Visit `https://your-sonar-url` → Check that your project appears and scan results show up.

---

## 6. Optional: Handling Large Reports

If you encounter:

```
ERROR: Failed to upload report - HTTP code 413: Request Entity Too Large
```

You need to increase `client_max_body_size` in **Nginx/Proxy config**:

```
client_max_body_size 50M;
```

Then reload your proxy:

```bash
sudo systemctl reload nginx
```

---

## 7. Notes

- SonarQube scans on `push to branch` and `merge` events.
- You **do not need to recreate** the project every time — once created, just reuse the `projectKey`.
- Multiple repos can use the **same global token**, provided the user has access.
