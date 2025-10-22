# TD&TP-Docker
## Question 1-1:Pourquoi passer les variables avec -e au run plutôt que dans le Dockerfile ?
ça nous évite d’écrire des mots de passe visible dans l’image  et permettre de changer la configuration à l’exécution sans reconstruire l’image.
## Question 1-2:Pourquoi faut-il un volume attaché au conteneur Postgres ?
Parce que sans volume, les données sont stockées dans le système de fichiers éphémère du conteneur et sont perdues si le conteneur est détruit. Un volume garantit la persistance sur le disque de l’hôte.
## Question1-3:
### Command
**Construire une image Docker à partir du Dockerfile présent dans le dossier courant.**
docker build -t postgres-db-img .

**Lancer un conteneur nommé postgres-db-ctr basé sur l’image postgres-db-img.Le port 5432 du conteneur est mappé sur le port 5432 de la machine hôte.**
docker run -d --name postgres-db-ctr -p 5432:5432 postgres-db-img

**Créer un réseau Docker pour permettre la communication entre plusieurs Postgres et Adminer.**
docker network create app-network

**Supprimer le conteneur existant et relancer Postgres en le connectant au réseau app-network.
Les variables d’environnement sont définies via -e pour éviter de les écrire dans le Dockerfile.**
docker rm -f postgres-db-ctr
docker run -d --name postgres-db-ctr --network app-network -p 5432:5432 `-e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd `postgres:17.2-alpine

**Démarrer Adminer (interface web) sur le même réseau afin de gérer la base de données PostgreSQL.
L’interface est accessible sur http://localhost:8090.**
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer

**Recompiler une image PostgreSQL contenant les scripts SQL d’initialisation (création de tables et insertion de données).**
docker build -t postgres-db-init-img .

**Supprimer l’ancien conteneur et relancer la base initialisée avec les scripts copiés dans l’image(voir 1-1 pour explication-e).**
docker rm -f postgres-db-ctr 
docker run -d --name postgres-db-ctr --network app-network -p 5432:5432 `-e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd `postgres-db-init-img

**Afficher les journaux du conteneur pour vérifier que les scripts SQL ont bien été exécutés au démarrage.**
docker logs postgres-db-ctr

**Supprimer le conteneur et le relancer avec un volume pour rendre les données persistantes sur le disque hôte.
Le dossier /my/own/datadir sur l’hôte sera utilisé pour stocker les données PostgreSQL.**
docker rm -f postgres-db-ctr
docker run -d --name postgres-db-ctr --network app-network -p 5432:5432 `
  -v /my/own/datadir:/var/lib/postgresql/data `
  -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd `
  postgres-db-init-img


### Dockerfile
FROM postgres:17.2-alpine
COPY initdb/ /docker-entrypoint-initdb.d/
