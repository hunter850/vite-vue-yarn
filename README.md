# Vite + GitHub Pages 自動部署設定指南

本文件紀錄如何設定 GitHub Actions 自動部署 Vite 專案到 GitHub Pages 的 actions yml 檔 (以 yarn 為例子)。

```yml
# workflow 名字
name: Deploy static content to Pages

on:
    # 推送至 master branch 觸發
    push:
        branches: ["master"]

    # Allows you to run this workflow manually from the Actions tab
    workflow_dispatch:

# Sets the GITHUB_TOKEN permissions to allow deployment to GitHub Pages
permissions:
    contents: read
    pages: write
    id-token: write

# Allow one concurrent deployment
concurrency:
    group: "pages"
    cancel-in-progress: true

jobs:
    # Single deploy job since we're just deploying
    deploy:
        environment:
            # environments 的名字
            name: github-pages
            url: ${{ steps.deployment.outputs.page_url }}
        runs-on: ubuntu-latest
        steps:
            - name: Checkout
              uses: actions/checkout@v4
            - name: Set up Node
              uses: actions/setup-node@v4
              with:
                  node-version: lts/*
                  cache: "yarn"
            - name: Install dependencies
              run: yarn install --frozen-lockfile
            - name: Build
              env:
                  # 在 environments 裡面設定的 secret 會提供 執行 build 時的 env variable
                  VITE_HELLO: ${{ secrets.VITE_HELLO }}
              run: yarn build
            - name: Setup Pages
              uses: actions/configure-pages@v5
            - name: Upload artifact
              uses: actions/upload-pages-artifact@v3
              with:
                  # Upload dist folder
                  path: "./dist"
            - name: Deploy to GitHub Pages
              id: deployment
              uses: actions/deploy-pages@v4
```
