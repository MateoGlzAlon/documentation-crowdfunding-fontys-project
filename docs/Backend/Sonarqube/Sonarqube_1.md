---
sidebar_position: 1
---

# SonarQube Integration with GitHub Actions

## Overview

This guide explains how to integrate **SonarQube** with **GitHub Actions** using a GitHub runner. Since GitHub runners cannot directly access `localhost:9000`, we use **Ngrok** to expose the local SonarQube instance to the web.

## Steps

### 1. Running SonarQube Locally

- Start SonarQube on `localhost:9000`. *(Note: Docker is recommended for running SonarQube locally)*.
- Access the SonarQube dashboard, create a new project, and obtain the **project key** and **authentication token**.

### 2. Exposing SonarQube with Ngrok

- Use Ngrok to expose your local SonarQube instance by running the following command:

```bash, title="Exposing port 9000 with Ngrok"
    ngrok http --url=deer-eternal-eagerly.ngrok-free.app 9000
```

- Ngrok will generate a public URL (e.g., `https://your-ngrok-subdomain.ngrok-free.app`). You can also configure a custom domain through the Ngrok dashboard if needed.
- Store the public Ngrok URL in your GitHub repository secrets as `SONAR_HOST_URL`.

### 3. Setting Up GitHub Actions Workflow

- Create a new GitHub Actions workflow file (`.github/workflows/sonarqube.yml`) in your repository with the following content:

```yaml, title=".github/workflows/sonarqube.yml"
name: SonarQube Analysis

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build and Analyze
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Shallow clones should be disabled for more relevant analysis

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: 17
          distribution: 'zulu' # Alternative distributions are available

      - name: Cache SonarQube packages
        uses: actions/cache@v4
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar
          restore-keys: ${{ runner.os }}-sonar

      - name: Cache Gradle packages
        uses: actions/cache@v4
        with:
          path: ~/.gradle/caches
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle') }}
          restore-keys: ${{ runner.os }}-gradle

      - name: Grant execution permissions for Gradlew
        run: chmod +x ./gradlew  # Grant execution permissions for the gradlew script

      - name: Build and Analyze with SonarQube
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
        run: ./gradlew build sonar --info
```

- Ensure that you replace:
  - `SONAR_HOST_URL` with your Ngrok URL stored as a GitHub secret.
  - `SONAR_TOKEN` with your SonarQube authentication token, also stored as a GitHub secret.

### 4. Running the Workflow

- Push any changes to your repository.
- The GitHub Actions workflow will automatically trigger, running the SonarQube analysis. The results will be visible on the SonarQube dashboard and in the GitHub Actions log.

## Notes

:::warning
- **Ngrok**: Ensure that Ngrok is running before initiating a scan so that SonarQube can be accessed.
:::


