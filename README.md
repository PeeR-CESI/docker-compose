# docker-compose
Tout ce qu'il faut pour déployer l'application web PeeR en utilisant Docker Compose.


## Configuration du Fichier hosts

Il est nécessaire de s'assurer que le nom de domaine "peer.cesi" pointe vers la bonne adresse IP (ici, localhost).

Pour cela, ajoutez une entrée dans le fichier hosts :

- Sur Windows, le fichier se trouve à `C:\Windows\System32\drivers\etc\hosts`.
- Sur MacOS ou Linux, il est situé à `/etc/hosts`.

Ajoutez la ligne suivante si elle n'est pas déjà présente :

```bash
127.0.0.1 peer.cesi
```

**N'oubliez pas de redémarrer votre ordinateur !**

Cette étape est cruciale pour s'assurer que lorsque vous envoyez une requête à `peer.cesi` depuis Postman, la requête est dirigée vers votre instance locale de Traefik.


## Cloner le repo

Clonez le dépôt avec la commande suivante :

```bash
git clone https://github.com/PeeR-CESI/docker-compose.git
```



## Déployer l'application

Pour déployer l'application dans l'environnement Docker, naviguez jusqu'à la racine du dossier cloné et exécutez cette commande :

```bash
docker compose up
```


Pour lancer l'application en mode détaché (les conteneurs fonctionneront en arrière-plan) :

```bash
docker compose up -d
```



## Voir les logs des conteneurs

Pour afficher la liste des conteneurs Docker actifs :

```bash
docker ps
```

Dans la sortie, notez l'ID du conteneur dont vous souhaitez voir les logs, puis utilisez :

```bash
docker logs {ID}
```


## Arrêter et Supprimer les Conteneurs

Pour arrêter les conteneurs en cours d'exécution :

```bash
docker compose down
```


## Nettoyer les Volumes

Si vous souhaitez supprimer les données persistantes et repartir de zéro, n'oubliez pas de supprimer aussi les volumes :

```bash
docker volume rm $(docker volume ls -q)
```


**Attention :** Cette commande supprimera tous les volumes Docker non utilisés par des conteneurs actifs. Si vous souhaitez supprimer uniquement les volumes spécifiques à ce projet, spécifiez leurs noms explicitement.

---

Suivez ces étapes pour configurer, déployer et gérer votre application web PeeR avec Docker Compose.
