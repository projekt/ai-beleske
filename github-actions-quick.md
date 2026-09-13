# GitHub Actions - Quick Reference

## Workflow osnova

```yaml
name: Ime workflow-a
on: [push, pull_request, schedule, workflow_dispatch]
jobs:
  job-name:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Hello"
```

## Triggeri

| Trigger | Opis |
|---------|------|
| `push` | Svaki push |
| `pull_request` | Na PR |
| `schedule: - cron: '0 0 * * *'` | Po rasporedu |
| `workflow_dispatch` | Ručno |
| `release` | Kad objaviš release |

## runneri

- `ubuntu-latest`
- `windows-latest`
- `macos-latest`

## Akcije

```yaml
- uses: actions/checkout@v4
- uses: actions/setup-node@v4
- uses: actions/setup-python@v4
- uses: actions/setup-java@v4
- uses: subosito/flutter-action@v2
- uses: actions/upload-artifact@v4
- uses: actions/download-artifact@v4
- uses: actions/github-script@v7
- uses: peaceiris/actions-gh-pages@v3
```

## Varijable

```yaml
env:
  GLOBAL_VAR: value

jobs:
  job:
    env:
      JOB_VAR: value
    steps:
      - run: echo ${{ env.GLOBAL_VAR }}
      - run: echo ${{ secrets.MY_SECRET }}
      - run: echo ${{ github.ref }}
      - run: echo ${{ github.event_name }}
      - run: echo ${{ github.sha }}
```

## Matrix

```yaml
strategy:
  matrix:
    node: [18, 20, 22]
    os: [ubuntu-latest, windows-latest]
steps:
  - uses: actions/setup-node@v4
    with:
      node-version: ${{ matrix.node }}
```

## Uslovi

```yaml
if: github.ref == 'refs/heads/main'
if: github.event_name == 'pull_request'
if: success()
if: failure()
```

## Čuvanje fajlova

```yaml
# Upload
- uses: actions/upload-artifact@v4
  with:
    name: my-artifact
    path: ./dist

# Download
- uses: actions/download-artifact@v4
  with:
    name: my-artifact
```

## Secrets

```yaml
${{ secrets.GITHUB_TOKEN }}  # Automatski
${{ secrets.MY_API_KEY }}    # Ručno dodat
```

## Primeri

### Node.js
```yaml
- uses: actions/setup-node@v4
  with: { node-version: '20' }
- run: npm ci
- run: npm test
```

### Flutter
```yaml
- uses: subosito/flutter-action@v2
  with: { flutter-version: '3.16.0' }
- run: flutter pub get
- run: flutter test
```

### Python
```yaml
- uses: actions/setup-python@v4
  with: { python-version: '3.11' }
- run: pip install -r requirements.txt
- run: pytest
```

### Docker
```yaml
- uses: docker/build-push-action@v5
  with:
    push: true
    tags: user/app:latest
```

### Deploy na Pages
```yaml
- uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./build
```

## GitHub CLI

```bash
gh workflow list
gh workflow run "Ime"
gh run list
gh run view <id>
gh run cancel <id>
```

## Cron sintaksa

```
┌───────── min (0-59)
│ ┌─────── sat (0-23)
│ │ ┌───── dan (1-31)
│ │ │ ┌─── mesec (1-12)
│ │ │ │ ┌─ dan (0-7, 0=7)
│ │ │ │ │
* * * * *
```

Primeri:
- `0 0 * * *` - svaki dan u ponoć
- `*/5 * * * *` - svakih 5 minuta
- `0 9 * * 1-5` - radnim danima u 9h
