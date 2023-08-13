# Nettoyage de Docker

> Conteneurs, images, et volumes prennent beaucoup d'espace disque. Ces commandes permettent un nettoyage en profondeur. Si vous possédez Docker Desktop en plus de Docker Engine, vous pouvez lancer ces commandes deux fois (avec et sans Docker Desktop d'ouvert).

## 1. Arrêter tous les conteneurs en cours d'exécution

```bash
docker stop $(docker ps -aq)
```

## 2. Supprimer tous les conteneurs

```bash
docker rm $(docker ps -aq)
```

## 3. Supprimer toutes les images

```bash
docker rmi $(docker images -q)
```

## 2. Supprimer un par un tous les volumes

```bash
for vol in $(docker volume ls -q)
do
  echo "Suppression du volume $vol"
  docker volume rm $vol
done
```

## 2. Tout purger

```bash
docker system prune -af
```