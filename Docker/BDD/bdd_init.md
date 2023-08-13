# Mise en place d'une base de données

### Postgres dans Docker (sans Docker Compose)

- Démarrer une base de données Postgres accessible par l'hôte via le port 5530 :

```
docker run --name pgdb -d -p 5530:5432 -e POSTGRES_PASSWORD=bidon postgres
```

- Accéder à la console CLI de la base de données :

```
docker exec -it pgdb psql -U postgres
```

### Postgres et Adminer avec Docker Compose (ficher fourni dans le dossier `Docker/BDD`)

- Démarrer une base de données Postgres et un client Adminer accessible par l'hôte sur http://localhost:8090/ :

```
docker-compose up
```

- Accéder à la console CLI de la base de données :

```
docker compose exec database psql -U postgres
```
