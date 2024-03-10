<div style="text-align: center;">

# Laravel Auto Deploy

Deploy Laravel Application to Server via SSH by RSync

</div>

This project is fork from [hypertech-lda/laravel-auto-deploy](https://github.com/hypertech-lda/laravel-auto-deploy) with some changes:

- [x] Remove sudo/chown commands;
- [x] Custom PHP binary path support (Useful with multiple PHP versions installed/available)
- [x] Add artisan migrate --force

## Default Artisan Commands
```
php artisan cache:clear 
php artisan route:cache
php artisan config:cache
php artisan migrate --force
```
All commands above are executed in the default order.

## Custom Artisan Commands
 Custom commands can be executed after default artisan command. Custom commands could be added in the `.github/workflows/deploy.yml` file.


## Config example:

.github/workflows/deploy.yml for gitea actions

```
name: Deploy to server
on:
  push:
    branches: [ "main" ]

jobs:
  build:
    name: Build & Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Environment
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
        env:
          COMPOSER_AUTH: ${{ secrets.COMPOSER_AUTH_JSON }}

      - name: Install Composer Packages
        run: composer install --no-dev
        env:
          COMPOSER_AUTH: ${{ secrets.COMPOSER_AUTH_JSON }}

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
        env:
          COMPOSER_AUTH: ${{ secrets.COMPOSER_AUTH_JSON }}

      - name: Install NPM dependencies
        run: npm install
        env:
          COMPOSER_AUTH: ${{ secrets.COMPOSER_AUTH_JSON }}

      - name: Compile assets for deploy
        run: npm run build
        env:
          COMPOSER_AUTH: ${{ secrets.COMPOSER_AUTH_JSON }}

      - name: Deploy To server
        uses: hagabardlaravel-auto-deploy@0.1
        with:
          user: ${{ vars.SERVER_USER }}
          host: ${{ vars.SERVER_HOST }}
          path: ${{ vars.SERVER_PATH }}
          port: ${{ vars.SERVER_PORT }}
          phpcmd: ${{ vars.PHP_CMD }}
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}



```
