name: Build and Deploy PHP to Azure Web App

on:
  push:
    branches:
      - main # Altere para 'master' se for a sua branch principal
  workflow_dispatch:

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Baixar código do repositório
        uses: actions/checkout@v4

      - name: Configurar PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2' # Ajuste para a versão do PHP do seu App Service

      # Descomente o bloco abaixo caso o seu projeto utilize Composer
      # - name: Instalar dependências do Composer
      #   run: composer install --no-dev --optimize-autoloader

      - name: Criar pacote ZIP limpo para o OneDeploy
        run: zip -r release.zip . -x "*.git*" "*.github*" "node_modules/*" "tests/*"

      - name: Login no Azure
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Deploy para Azure Web App
        uses: azure/webapps-deploy@v3
        with:
          app-name: 'meexemplousitephp--gef8a5cgapa9aucy'
          package: release.zip

