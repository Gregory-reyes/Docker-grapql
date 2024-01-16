# Configuración y despliegue

## Restricciones en github
settings -> secrets -> Actions -> New repository secret

## creación de 2 secrets en docker hub

1. name= DOCKER_USER
2. secret = secreto de docker hub

## creación del password con token de acceso docker hub -> account settings -> security -> new access token ->asigno permisos -> generar token

1. name= DOCKER_PASSWORD
2. secret = token de acceso docker hub

## ejecutamos el comando para crear la imagen
```
docker build -t gregoryreyesp/docker-graphql:1.0.0 .
```

## ejecutamos el comando para crear el contenedor y habiliar el puerto 3000
```
docker container run -p 3000:3000 gregoryreyesp/docker-graphql:1.0.0
```

## editamos el archivo en actions github -> workflows -> docker-image.yml con el siguiente codigo probado y funcional 
```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:

  build:

    runs-on: ubuntu-latest

    steps:
    - name: checkout code
      uses: actions/checkout@v3
      with:
        fetch-depth: 0
        
    - name: Docker login
      env:
        DOCKER_USER: ${{ secrets.DOCKER_USER }}
        DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      run: |      
        echo ${{ secrets.DOCKER_PASSWORD }} | docker login --username ${{ secrets.DOCKER_USER }} --password-stdin
        
    - name: Build Docker Image
      run: |
        docker build -t gregoryreyesp/docker-graphql:1.0.1 .
        docker build -t gregoryreyesp/docker-graphql:latest .
    - name: push Docker Image
      run: |
        docker push gregoryreyesp/docker-graphql:1.0.1
        docker push gregoryreyesp/docker-graphql:latest

```

