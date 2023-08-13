# Nettoyage d'Ubuntu

> Guide de nettoyage du système d'exploitation Ubuntu afin de gagner de l'espace disque.

### 1. Suppression des fichiers temporaires des fichiers d'installation

> Supprimez les paquets .deb pour gagner de la place, car après plusieurs installations les paquets téléchargés s'accumulent et utilisent beaucoup d'espace. Ces paquets d'installation sont sauvegardés dans le dossier `/var/cache/apt/archives/`

- Supprimer le cache des paquets périmés

```bash
sudo apt autoclean
```

- Supprimer tout le cache

```bash
sudo apt clean
```

- Supprimer les paquets installés automatiquement comme dépendances et devenus inutiles

```bash
sudo apt autoremove
```

### 2. Suppression des résidus de configuration de logiciels supprimés

> Il arrive qu'après suppression d'une application, des résidus de configuration ne soient pas supprimés.

- Voir la liste des résidus de configuration

```bash
apt list ~c
```

- Supprimer tous les résidus de configuration

```bash
sudo apt purge ~c
```

### 3. Vider la corbeille

- Vider la corbeille de l'espace personnel

```bash
rm -r -f ~/.local/share/Trash/*/*
```

### 4. Supprimer les miniatures des images

> Commande pour supprimer les miniatures d'images de plus de 7 jours.

```bash
find ~/.cache/thumbnails -type f -atime +7 -delete
```

### 5. Supprimer des fichiers de sauvegarde

> Ce sont les fichiers cachés qui se terminent par un "~". Ils servent de sauvegarde au cas où le fichier source (texte, html, php, conf, … ) serait supprimé ou modifié accidentellement. Ils s'accumulent, s'accumulent…

- Voir les fichiers à supprimer

> On rajoute `less` pour une meilleure lisibilité lorsque la liste est très longue. Pour sortir du `less`, appuyer sur la touche `Q`.

```bash
find ~/ -name '*~' | less -S
```

- Supprimer sans confirmation

```bash
find ~/ -name '*~' -print0 | xargs -0 rm
```

### 6. Supprimer les versions désactivées des snap

> Les paquets Format Snap sont des applications "tout en un" qui se mettent à jour un peu indépendamment du système principal (on peut avoir ainsi la dernière version logicielle sur un système plus ancien). Mais lors de la mise à jour vers une nouvelle version, une ou deux versions précédentes restent en place, désactivées. Comme ces paquets contiennent leurs propres dépendances, ils peuvent être assez volumineux.

- Voir la liste des versions des snap

```bash
snap list --all
```

- Supprimer l'ensemble des versions désactivées (avec bash)

```bash
LANG=C
echo "Suppression des anciennes révisions de Snap :"
sudo snap list --all | awk '/disabled/{print $1, $3}' |
while read snapname revision; do
    echo "$snapname"
    sudo snap remove "$snapname" --revision="$revision"
    echo "- Snap $snapname ($revision) supprimé"
done
```