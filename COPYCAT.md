# Tutorial 

## Add Remote  

- SOURCE CONTROL > Remote > Add Remote  

```bash
git remote
git fetch blog
git checkout devkimchi
```

## Pre-build  

### Bootstrapper  

```bash
pwd
# Confirm that we are in `/workspaces/ddd-seoul-2024`
mkdir srcs && cd srcs
hugo new site .
```

### Submodule and only get themes  

```bash
pwd
# Confirm that we are in `/workspaces/ddd-seoul-2024/srcs`
git submodule add --depth 1 https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
git rm --cached themes/ananke
rm ../.gitmodules
echo "theme = 'ananke'" >> hugo.toml
```

### Copy only dependancies

```bash
pwd
# Confirm that we are in `/workspaces/ddd-seoul-2024/srcs`
cp -r . ..
```

### Delete srcs

```bash
pwd
# Confirm that we are in `/workspaces/ddd-seoul-2024/srcs`
cd ..
rm -r srcs
```

### Update hugo.toml

```bash
baseURL = 'https://<USERNAME>.github.io/'  
languageCode = 'ko-kr'  
title = '<TYPE WHAT YOU WANT>'  
theme = 'ananke'  
```

### Update archetypes

- open archetypes/default.md
  - Set disable draft mode
  - Some Guidance

```yaml
---
date: '{{ .Date }}'
draft: false
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
---

## REPLACE TITLE HERE

여기에 텍스트 입력
```

### Upload to <USERNAME>.github.io repository  

```bash
pwd
# Confirm that we are in `/workspaces/ddd-seoul-2024`
git status
git checkout -b ephemeral blog/main
git add .
git status
git commit -m "update: pre-build resources"
git push blog ephemeral: main
```

## GitHub Actions

### Create folder and file  

```bash
mkdir -p .github/workflows && cd .github/workflows
touch deploy.yml
```

- Set Indent with SPACE 2
  - Not Tab 4

### Pre-requisite

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
  # workflow_dispatch:

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.139.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget --progress=bar:force:noscroll -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb  
          DEBIAN_FRONTEND=noninteractive sudo dpkg -i ${{ runner.temp }}/hugo.deb
```

### Job 1

```yaml
      - uses: actions/checkout@v4
        name: Checkout main branch
        with:
          ref: main
          fetch-depth: 0
          submodules: recursive
      
      - name: Build Website with Hugo
        run: hugo
```

### Job 2

```yaml
      - uses: actions/checkout@v4
        name: Checkout gh-pages repo
        with:
          ref: gh-pages
          fetch-depth: 0
          path: emerald

      - name: Copy main/public To gh-pages/emerald
        working-directory: emerald
        run: |
          rm -rf *
          cp -rf ../public/* .
```

### Job 3

```yaml
      - name: Commit Website Updates
        working-directory: emerald
        run: |
          git config --global user.name github-actions
          git config --global user.email github-actions@github.com
          git add .
          git diff-index --quiet HEAD || git commit -m "Deploy website updates with GitHub Actions: ${GITHUB_SHA}"
          git push origin gh-pages
```

### Full Actions  

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
  # workflow_dispatch:

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.139.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget --progress=bar:force:noscroll -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb  
          DEBIAN_FRONTEND=noninteractive sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - uses: actions/checkout@v4
        name: Checkout main branch
        with:
          ref: main
          fetch-depth: 0
          submodules: recursive
      
      - name: Build Website with Hugo
        run: hugo

      - uses: actions/checkout@v4
        name: Checkout gh-pages repo
        with:
          ref: gh-pages
          fetch-depth: 0
          path: emerald

      - name: Copy main/public To gh-pages/emerald
        working-directory: emerald
        run: |
          rm -rf *
          cp -rf ../public/* .
      
      - name: Commit Website Updates
        working-directory: emerald
        run: |
          git config --global user.name github-actions
          git config --global user.email github-actions@github.com
          git add .
          git diff-index --quiet HEAD || git commit -m "Deploy website updates with GitHub Actions: ${GITHUB_SHA}"
          git push origin gh-pages
```

## Chores  

### badge

```markdown
![workflow status](https://github.com/<USERNAME>/<USERNAME>.github.io/actions/workflows/deploy.yml/badge.svg)  
```

### boundary

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
    paths: [ contents ]
```

### favicon

- themes/ananke/layouts/partials/site-favicon.html
- hugo.toml
- ~~themes/ananke/config/_default/params.toml~~  

```html
<link rel="shortcut icon" href="{{ relURL ($.Site.Params.favicon) }}" type="image/x-icon" />
```

```toml
baseURL = 'https://<USERNAME>.github.io/'  
languageCode = 'ko-kr'  
title = '<TYPE WHAT YOU WANT>'  
theme = 'ananke'

[Params]
  favicon = "/img/favicon/<image-name>.png"

```

```bash
git add .
git commit -m "update: favicon"
git push blog ephemeral:main
```

### etc.  

-giscus: <https://giscus.app/ko>  
- uxwing: <https://uxwing.com/>  
