+++
title = 'Creating a Minimalist Website with Hugo'
date = 2024-10-12T17:10:03-03:00
draft = false

author = "Cluyverth W."
showToc = true
TocOpen = false
hidemeta = false
comments = false
description = ""
canonicalURL = "https://canonical.url/to/page"
disableHLJS = true # to disable highlightjs
disableShare = false
hideSummary = false
searchHidden = false
ShowReadingTime = true
ShowBreadCrumbs = true
ShowPostNavLinks = true
ShowWordCount = true
ShowRssButtonInSectionTermList = true
UseHugoToc = true
tags = ["HelloWorld", "Hugo", "Go"]
+++

## Step 1: Install Hugo
To install Hugo, you can use `winget`:

```bash
winget install Hugo.Hugo.Extended
```

## Step 2: Create a New Hugo Site
Once Hugo is installed, create a new site by running:

```bash
hugo new site YourSite --format yaml
```

This will generate a new Hugo project in the `YourSite` folder.

## Step 3: Download the PaperMod Theme

To use the PaperMod theme, you need to clone it into your `themes` directory. Go to the PaperMod GitHub repository for the official documentation.

Run the following command inside `YourSite` folder to clone the theme:

```bash
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```

After cloning, navigate to the themes/PaperMod folder. To keep the theme up to date, run a pull command to get the latest changes:

```bash
cd themes/PaperMod
git pull
```

## Step 4: Update config.yaml

Now, in the root of your Hugo project, update the `config.yaml` file to use the PaperMod theme. Add the following line:

```yaml
theme: ["PaperMod"]
```

## Step 5: Set Up GitHub Pages and GitHub Actions

To automate the deployment to GitHub Pages using GitHub Actions, follow the steps below.

### 5.1: Create the .github folder

Inside your project root, create a `.github` folder and a `workflows` directory inside it:

```bash
mkdir -p .github/workflows
```

### 5.2: Add the GitHub Actions Workflow

Inside the `workflows` folder, create a file named `hugo.yaml` and paste the following content:

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.134.2
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: America/Los_Angeles
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

This workflow will automatically build and deploy your Hugo site to GitHub Pages every time you push to the `main` branch.

### 5.3: Create .gitignore

Create a `.gitignore` file in the root of your project with the following contents to exclude unnecessary files from your repository:

```bash
public/
resources/
.hugo_build.lock
```

### 5.4: Add .gitmodules

Create a `.gitignore` file in the root directory and add the PaperMod submodule reference:

```bash
[submodule "themes/PaperMod"]
    path = themes/PaperMod
    url = https://github.com/adityatelange/hugo-PaperMod.git
```

Step 6: Start Customizing Your Website

Once everything is set up, you can begin customizing your theme, adding content, and modifying layouts to suit your needs. The PaperMod documentation provides many customization options, so check that out for more details.