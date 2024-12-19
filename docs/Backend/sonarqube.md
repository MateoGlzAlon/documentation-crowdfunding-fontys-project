---
sidebar_position: 3
sidebar_label: Sonarqube analysis
---

# SonarCloud Integration with GitHub Actions

## Overview

This guide explains how to integrate **SonarCloud** with **GitHub Actions** using a GitHub runner.

## Steps

### 1. Setting Up SonarCloud

- Create an account on [SonarCloud](https://sonarcloud.io/) if you don't already have one.
- Create a new project in SonarCloud.

### 2. Configuring GitHub Repository Secrets

- Store the following secret in your GitHub repository:
  - `SONAR_TOKEN`: Your SonarCloud authentication token.

### 3. Setting Up GitHub Actions Workflow

- Create a new GitHub Actions workflow file (`.github/workflows/sonarcloud.yml`) in your repository with the following content:

```yaml, title=".github/workflows/sonarqube.yml"
name: SonarQube
on:
  push:
    branches:
      - master
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  build:
    name: Build and analyze
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Shallow clones should be disabled for a better relevancy of analysis
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: 17
          distribution: 'zulu' # Alternative distribution options are available
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
      - name: Build and analyze
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Needed to get PR information, if any
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        run: ./gradlew build sonar --info
```

### 4. Running the Workflow

- Push any changes to your repository.
- The GitHub Actions workflow will automatically trigger, running the SonarCloud analysis. The results will be visible on the SonarCloud dashboard and in the GitHub Actions log.

## Notes

:::warning
- **Authentication**: Ensure your GitHub repository secrets are configured correctly to avoid authentication failures.
:::