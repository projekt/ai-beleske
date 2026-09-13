# GitHub Actions - Opsirna dokumentacija

## Sadržaj
1. [Uvod](#uvod)
2. [Workflow fajl](#workflow-fajl)
3. [Triggeri](#triggeri)
4. [Jobs i Steps](#jobs-i-steps)
5. [Runneri](#runneri)
6. [Akcije](#akcije)
7. [Varijable i Secrets](#varijable-i-secrets)
8. [Matrix strategija](#matrix-strategija)
9. [Uslovi i izrazi](#uslovi-i-izrazi)
10. [Artifacts](#artifacts)
11. [Caching](#caching)
12. [Environments](#environments)
13. [Reusable workflows](#reusable-workflows)
14. [Self-hosted runneri](#self-hosted-runneri)
15. [Bezbednost](#bezbednost)
16. [Debugging](#debugging)
17. [Primeri](#primeri)
18. [Greške i rešenja](#greške-i-rešenja)

---

## Uvod

GitHub Actions je CI/CD platforma koja automatizuje build, test i deploy procese. Pokreće se na GitHub serverima ili na tvom sopstvenom serveru.

### Ključni koncepti

- **Workflow** - Automatizovani proces (YAML fajl)
- **Event** - Ono što pokreće workflow (push, PR, itd.)
- **Job** - Skup koraka koji se izvršavaju na istom runneru
- **Step** - Pojedinačna akcija ili komanda
- **Action** - Ponovo upotrebljiva komponenta
- **Runner** - Server koji izvršava jobove

### Struktura

```
.github/
└── workflows/
    ├── ci.yml
    ├── cd.yml
    └── auto-delete.yml
```

---

## Workflow fajl

### Minimalni primer

```yaml
name: Moj Workflow

on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Zdravo!"
```

### Osnovna struktura

```yaml
name: Ime workflow-a        # Ime (opciono)

on: trigger                 # Kada se pokreće

env:                         # Globalne varijable
  KEY: value

permissions:                 # Dozvole
  contents: read

jobs:                        # Poslovi
  job1:
    runs-on: ubuntu-latest
    env:
      JOB_KEY: value
    steps:
      - uses: akcija@v1
      - run: komanda
      
  job2:
    needs: job1              # Zavisi od job1
    runs-on: ubuntu-latest
    steps:
      - run: echo "Job 2"
```

---

## Triggeri

### Osnovni triggeri

```yaml
on:
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - '!src/test/**'  # Isključi
    tags:
      - 'v*'
  
  pull_request:
    branches: [main]
    types: [opened, synchronize, reopened]
  
  schedule:
    - cron: '0 2 * * 1-5'  # Radnim danima u 2h
  
  workflow_dispatch:
    inputs:
      environment:
        description: 'Okruženje'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
```

### Napredni triggeri

```yaml
on:
  # Push samo na određene fajlove
  push:
    paths:
      - '**.js'
      - 'package.json'
    paths-ignore:
      - '**.md'
      - 'docs/**'

  # PR samo kad je spreman
  pull_request:
    types: [ready_for_review]

  # Workflow completion
  workflow_run:
    workflows: ["Build"]
    types: [completed]
    branches: [main]

  # Webhook događaji
  repository_dispatch:
    types: [deploy-command]
```

### Trigger iz GitHub API-ja

```bash
# Pokreni workflow
curl -X POST \
  -H "Authorization: token ghp_xxxx" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/workflow_id/dispatches \
  -d '{"ref":"main","inputs":{"environment":"production"}}'
```

---

## Jobs i Steps

### Jobs

```yaml
jobs:
  # Jednostavan job
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Test"

  # Job sa uslovom
  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    needs: test
    steps:
      - run: echo "Deploy"

  # Paralelni jobovi
  build-linux:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Linux"
  
  build-windows:
    runs-on: windows-latest
    steps:
      - run: echo "Windows"

  # Job koji zavisi od više jobova
  release:
    needs: [build-linux, build-windows]
    runs-on: ubuntu-latest
    steps:
      - run: echo "Release"
```

### Steps

```yaml
steps:
  # Git checkout
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0  # Sva istorija
      token: ${{ secrets.GITHUB_TOKEN }}

  # Pokretanje komande
  - run: echo "Hello World"
    env:
      MY_VAR: "value"
    working-directory: ./src

  # Instalacija paketa
  - name: Install dependencies
    run: |
      npm ci
      npm install -g typescript

  # Conditional step
  - name: Deploy
    if: success() && github.ref == 'refs/heads/main'
    run: ./deploy.sh

  # Continue on error
  - name: Risky step
    continue-on-error: true
    run: ./risky-command.sh

  # Timeout
  - name: Long task
    timeout-minutes: 30
    run: ./long-task.sh
```

---

## Runneri

### GitHub-hosted runneri

| Runner | OS | RAM | Disk |
|--------|-----|-----|------|
| `ubuntu-latest` | Ubuntu 22.04 | 7 GB | 14 GB |
| `ubuntu-22.04` | Ubuntu 22.04 | 7 GB | 14 GB |
| `ubuntu-20.04` | Ubuntu 20.04 | 7 GB | 14 GB |
| `windows-latest` | Windows Server 2022 | 7 GB | 14 GB |
| `macos-latest` | macOS 14 | 3 GB | 14 GB |
| `macos-13` | macOS 13 | 3 GB | 14 GB |

### ARM runneri

```yaml
runs-on: ubuntu-latest
# ili
runs-on: [self-hosted, linux, ARM64]
```

### Labels za self-hosted

```yaml
runs-on: [self-hosted, linux, x64, gpu]
```

---

## Akcije

### Korišćenje akcija

```yaml
# Lokalna akcija
- uses: ./.github/actions/my-action

# GitHub akcija
- uses: actions/checkout@v4

# Docker akcija
- uses: docker://alpine:3.18

# Kompletna specifikacija
- uses: actions/checkout@v4.1.1
  with:
    repository: owner/repo
    ref: main
    path: ./subdir
    fetch-depth: 1
```

### Popularne akcije

```yaml
# Git
- uses: actions/checkout@v4

# Node.js
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'

# Python
- uses: actions/setup-python@v5
  with:
    python-version: '3.11'

# Java
- uses: actions/setup-java@v4
  with:
    distribution: 'temurin'
    java-version: '17'

# Flutter
- uses: subosito/flutter-action@v2
  with:
    flutter-version: '3.16.0'

# Docker
- uses: docker/setup-buildx-action@v3
- uses: docker/build-push-action@v5

# Artifacts
- uses: actions/upload-artifact@v4
- uses: actions/download-artifact@v4

# GitHub Pages
- uses: peaceiris/actions-gh-pages@v3

# Codecov
- uses: codecov/codecov-action@v3
```

### Kreiranje sopstvene akcije

```yaml
# .github/actions/setup-project/action.yml
name: 'Setup Project'
description: 'Postavlja projekat'
inputs:
  node-version:
    description: 'Node.js verzija'
    default: '20'
outputs:
  version:
    description: 'Verzija Node.js'
    value: ${{ steps.setup.outputs.node-version }}
runs:
  using: 'composite'
  steps:
    - name: Setup Node.js
      id: setup
      uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
    - run: npm ci
      shell: bash
```

Korišćenje:
```yaml
- uses: ./.github/actions/setup-project
  with:
    node-version: '20'
```

---

## Varijable i Secrets

### Varijable

```yaml
# Globalne varijable
env:
  NODE_ENV: production
  API_URL: https://api.example.com

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      BUILD_TYPE: release
    steps:
      - run: echo ${{ env.NODE_ENV }}
      - run: echo ${{ env.BUILD_TYPE }}
      
      # Postavljanje varijable u koraku
      - name: Set variable
        run: echo "MY_VAR=hello" >> $GITHUB_ENV
      
      # Korišćenje u sledećem koraku
      - run: echo $MY_VAR
```

### Specijalne varijable

```yaml
${{ github.workspace }}        # Putanja do workspace-a
${{ github.event_name }}       # Ime event-a
${{ github.ref }}              # Branch/tag ref
${{ github.sha }}              # Commit SHA
${{ github.actor }}            # Ko je pokrenuo
${{ github.repository }}       # owner/repo
${{ github.event.head_commit.message }}  # Commit poruka
${{ github.run_id }}          # ID run-a
${{ github.run_number }}      # Broj run-a
${{ github.server_url }}      # https://github.com
${{ github.token }}           # Token (isti kao GITHUB_TOKEN)
```

### Secrets

```yaml
# Pristup secrets
${{ secrets.GITHUB_TOKEN }}           # Automatski
${{ secrets.MY_API_KEY }}             # Ručno dodat
${{ secrets.DOCKERHUB_TOKEN }}        # Docker Hub
${{ secrets.AWS_ACCESS_KEY_ID }}      # AWS
${{ secrets.SLACK_WEBHOOK_URL }}      # Slack

# Postavljanje secrets
# Settings → Secrets and variables → Actions → New repository secret

# Korišćenje u environmentu
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: echo ${{ secrets.PRODUCTION_SECRET }}
```

### Variables

```yaml
# Settings → Secrets and variables → Actions → Variables
${{ vars.MY_VARIABLE }}

# Korišćenje
steps:
  - run: echo ${{ vars.APP_NAME }}
```

---

## Matrix strategija

### Osnovna matrica

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [18, 20, 22]
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

### Napredna matrica

```yaml
strategy:
  matrix:
    include:
      - os: ubuntu-latest
        node: 20
        experimental: false
      - os: windows-latest
        node: 22
        experimental: true
    fail-fast: false  # Ne prekidaj druge jobove
```

### Matrica sa uslovima

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [18, 20]
    exclude:
      - os: windows-latest
        node: 18
    include:
      - os: macos-latest
        node: 20
```

### Dinamička matrica

```yaml
jobs:
  set-matrix:
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.set.outputs.matrix }}
    steps:
      - id: set
        run: |
          echo 'matrix={"os":["ubuntu-latest"],"node":[18,20]}' >> $GITHUB_OUTPUT
  
  test:
    needs: set-matrix
    runs-on: ${{ matrix.os }}
    strategy:
      matrix: ${{ fromJson(needs.set-matrix.outputs.matrix) }}
```

---

## Uslovi i izrazi

### Osnovni uslovi

```yaml
steps:
  - name: Deploy
    if: github.ref == 'refs/heads/main'
    run: ./deploy.sh

  - name: Test
    if: github.event_name == 'pull_request'
    run: npm test

  - name: Release
    if: startsWith(github.ref, 'refs/tags/v')
    run: npm publish
```

### Napredni uslovi

```yaml
steps:
  # Prethodni korak je uspeo
  - name: Success only
    if: success()
    run: echo "Uspeh"

  # Prethodni korak nije uspeo
  - name: Failure only
    if: failure()
    run: echo "Neuspeh"

  # Bilo koji prethodni korak
  - name: Always
    if: always()
    run: echo "Uvek"

  # Cancelovan
  - name: Cancelled
    if: cancelled()
    run: echo "Cancelovan"

  # Step output
  - name: Check
    id: check
    run: |
      echo "status=success" >> $GITHUB_OUTPUT
  
  - name: Use output
    if: steps.check.outputs.status == 'success'
    run: echo "OK"
```

### Izrazi

```yaml
# String operacije
if: startsWith(github.ref, 'refs/heads/')
if: endsWith(github.ref, '.md')
if: contains(github.event.head_commit.message, 'deploy')
if: "!contains(github.event.head_commit.message, 'skip')"

# Numeričke operacije
if: github.run_number > 5

# Kombinacije
if: >
  github.ref == 'refs/heads/main' &&
  github.event_name == 'push' &&
  success()
```

---

## Artifacts

### Čuvanje fajlova

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
      
      # Čuvanje celog direktorijuma
      - uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: ./dist
          retention-days: 7  # Čuvaj 7 dana
      
      # Čuvanje više fajlova
      - uses: actions/upload-artifact@v4
        with:
          name: multiple-files
          path: |
            ./dist
            ./build
            !./node_modules
      
      # Sa kompresijom
      - uses: actions/upload-artifact@v4
        with:
          name: compressed
          path: ./dist
          compression-level: 6
```

### Preuzimanje

```yaml
jobs:
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build-output
          path: ./dist
      
      # Preuzmi sve artifacts
      - uses: actions/download-artifact@v4
        with:
          path: ./all-artifacts
          merge-multiple: true
```

### Artifacts u PR komentarima

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: ./coverage

- uses: marocchino/sticky-pull-request-comment@v2
  with:
    message: |
      Coverage report: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
```

---

## Caching

### npm cache

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'

# Ili ručno
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-npm-
```

### Python cache

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.11'
    cache: 'pip'
```

### Flutter cache

```yaml
- uses: subosito/flutter-action@v2
  with:
    flutter-version: '3.16.0'
    cache: true
```

### Custom cache

```yaml
- uses: actions/cache@v4
  id: cache
  with:
    path: |
      ~/.cache/pub
      .dart_tool
    key: ${{ runner.os }}-dart-${{ hashFiles('**/pubspec.lock') }}
    restore-keys: |
      ${{ runner.os }}-dart-

- name: Install dependencies
  if: steps.cache.outputs.cache-hit != 'true'
  run: flutter pub get
```

---

## Environments

### Definisanje

```yaml
jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - run: echo "Deploy to staging"

  deploy-production:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: echo "Deploy to production"
```

### Zaštita environments

```yaml
# Settings → Environments → production
# - Required reviewers
# - Wait timer
# - Branch restrictions

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://example.com
    steps:
      - run: echo "Deployed"
```

### Environment variables

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: echo ${{ vars.APP_URL }}
      - run: echo ${{ secrets.DB_PASSWORD }}
```

---

## Reusable workflows

### Kreiranje

```yaml
# .github/workflows/reusable-build.yml
name: Reusable Build

on:
  workflow_call:
    inputs:
      node-version:
        required: false
        type: string
        default: '20'
    outputs:
      build-id:
        description: "Build ID"
        value: ${{ jobs.build.outputs.id }}
    secrets:
      API_KEY:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      id: ${{ steps.build.outputs.id }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      - run: npm ci && npm run build
      - id: build
        run: echo "id=$RUN_ID" >> $GITHUB_OUTPUT
```

### Korišćenje

```yaml
jobs:
  build:
    uses: ./.github/workflows/reusable-build.yml
    with:
      node-version: '22'
    secrets:
      API_KEY: ${{ secrets.API_KEY }}
```

---

## Self-hosted runneri

### Instalacija

```bash
# Preuzmi runner
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz

# Konfiguriši
./config.sh --url https://github.com/OWNER/REPO --token XXXXXXX

# Pokreni
./run.sh
```

### Kao servis

```bash
sudo ./svc.sh install
sudo ./svc.sh start
sudo ./svc.sh status
```

### Korišćenje

```yaml
runs-on: self-hosted
# ili
runs-on: [self-hosted, linux, x64]
```

---

## Bezbednost

### OIDC (OpenID Connect)

```yaml
jobs:
  deploy:
    permissions:
      id-token: write
      contents: read
    steps:
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789:role/my-role
          aws-region: eu-west-1
```

###最小权限

```yaml
permissions:
  contents: read
  packages: write
  issues: write
  pull-requests: write
```

### Čuvanje secrets

```yaml
# Nikad ne loguj secrets
- run: |
    echo "Token: ***"  # Ne koristi ${{ secrets.TOKEN }}

# Koristi environment
jobs:
  deploy:
    environment: production
    steps:
      - run: echo ${{ secrets.PROD_SECRET }}
```

---

## Debugging

### enabling debug logging

```yaml
# Settings → Secrets → Actions
# ADD: ACTIONS_STEP_DEBUG = true
# ADD: ACTIONS_RUNNER_DEBUG = true

steps:
  - run: echo "Debug mode"
```

### Re-running jobs

```bash
# GitHub CLI
gh run rerun <run-id>
gh run rerun <run-id> --failed
```

### Logging

```yaml
steps:
  - name: Debug
    run: |
      echo "Event: ${{ github.event_name }}"
      echo "Ref: ${{ github.ref }}"
      echo "SHA: ${{ github.sha }}"
      env  # Ispisi sve varijable
```

### debugging sa tmate

```yaml
- uses: mxschmitt/action-tmate@v3
  with:
    limit-access-to-actor: true
```

---

## Primeri

### 1. Full Stack CI/CD

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm test
      - run: npm run lint
      - uses: codecov/codecov-action@v3

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: build
          path: ./dist

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build
      - run: ./deploy-staging.sh

  deploy-production:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build
      - run: ./deploy-production.sh
```

### 2. Docker Build i Push

```yaml
name: Docker

on:
  push:
    tags: ['v*']

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: docker/setup-buildx-action@v3
      
      - uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      
      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: myapp
          tags: |
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
      
      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### 3. Release automations

```yaml
name: Release

on:
  push:
    tags: ['v*']

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - run: npm ci
      - run: npm test
      - run: npm run build
      
      - uses: softprops/action-gh-release@v1
        with:
          generate_release_notes: true
          files: |
            ./dist/*
      
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### 4. Scheduled maintenance

```yaml
name: Maintenance

on:
  schedule:
    - cron: '0 2 * * 1'  # Ponedeljak u 2h
  workflow_dispatch:

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Delete old artifacts
        uses: actions/github-script@v7
        with:
          script: |
            const artifacts = await github.rest.actions.listArtifactsForRepo({
              owner: context.repo.owner,
              repo: context.repo.repo,
              per_page: 100
            });
            
            for (const artifact of artifacts.data.artifacts) {
              const days = (Date.now() - new Date(artifact.created_at)) / (1000 * 60 * 60 * 24);
              if (days > 30) {
                await github.rest.actions.deleteArtifact({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  artifact_id: artifact.id
                });
              }
            }
```

---

## Greške i rešenja

### Česte greške

| Greška | Rešenje |
|--------|---------|
| `Error: Input required and not supplied: node-version` | Dodaj `with: node-version: '20'` |
| `No such file or directory` | Proveri `working-directory` |
| `Permission denied` | Dodaj `chmod +x` ili koristi `shell: bash` |
| `Rate limit exceeded` | Koristi cache ili sačekaj |
| `Secret not found` | Proveri Settings → Secrets |

### Debugging koraci

```yaml
steps:
  - name: Debug
    run: |
      echo "PWD: $(pwd)"
      echo "Files:"
      ls -la
      echo "Env:"
      env | sort
```

### Čišćenje

```bash
# Obriši stare workflow runs
gh api repos/{owner}/{repo}/actions/runs \
  --paginate -q '.workflow_runs[] | select(.status=="completed") | .id' \
  | xargs -I {} gh api -X DELETE repos/{owner}/{repo}/actions/runs/{}
```

---

## Resursi

- [GitHub Actions dokumentacija](https://docs.github.com/en/actions)
- [GitHub Actions marketplace](https://github.com/marketplace?type=actions)
- [GitHub Actions cheat sheet](https://github.github.io/cheatsheets/)
- [Action security best practices](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
