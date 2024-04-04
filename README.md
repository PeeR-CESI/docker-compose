# docker-compose
Tout ce qu'il faut pour déployer l'application web PeeR en utilisant Docker Compose et pour tester les microservices (api, bdd, routes,...).


## 1.Configuration du Fichier hosts

**À faire qu'une seule fois !**

Il est nécessaire de s'assurer que le nom de domaine "peer.cesi" pointe vers la bonne adresse IP (ici, localhost).

Pour cela, ajoutez une entrée dans le fichier hosts (ouvrir en tant qu'administrateur) :

- Sur Windows, le fichier se trouve à `C:\Windows\System32\drivers\etc\hosts`.
- Sur MacOS ou Linux, il est situé à `/etc/hosts`.

Ajoutez la ligne suivante si elle n'est pas déjà présente :

```bash
127.0.0.1 peer.cesi
```

**N'oubliez pas de redémarrer votre ordinateur !**

Cette étape est cruciale pour s'assurer que lorsque vous envoyez une requête à `peer.cesi` depuis Postman, la requête est dirigée vers votre instance locale de Traefik.


## 2. Cloner le repo

Clonez le dépôt avec la commande suivante :

```bash
git clone https://github.com/PeeR-CESI/docker-compose.git
```



## 3. Décommenter les services et spécifier la version à déployer

Décommentez les services que vous voulez déployer/tester dans le fichier docker-compose.yaml, les services représentent les conteneurs docker, et donc les microservices de l'application.

Par exemple, vous voulez tester le services "user-management", on passe de ça :

```bash
  # user-management:
  #   image: xpapaya/user-management:v0.1.1
  #   depends_on:
  #     - postgresql
  #   restart : unless-stopped
  #   networks:
  #     - microservices
```

à ça (ctrl + / sur le bloc de code dans VScode) :
**Verifiez l'indentation !**

```bash
  user-management:
    image: xpapaya/user-management:v0.1.1
    depends_on:
      - postgresql
    restart : unless-stopped
    networks:
      - microservices
```

Ensuite, spécifiez la version de l'image Docker du microservice à déployer. Celle-ci correspond généralement à la dernière version de la "dev-X" et lorsque celle-ci est testée, à la release associée "vX.Y.Z :

```bash
  user-management:
    image: xpapaya/user-management:v0.1.1
    depends_on:
      - postgresql
    restart : unless-stopped
    networks:
      - microservices
```

en testant par exemple la "dev-12":

```bash
  user-management:
    image: xpapaya/user-management:dev-12
    depends_on:
      - postgresql
    restart : unless-stopped
    networks:
      - microservices
```

**Ne pas oublier les blocs de bases de données !**

**Vous pouvez décommenter autant de services que vous le souhaitez, à partir du moment où les routes sont définies et que les versions sont testées**


## 4. Déployer tout ou partie de l'application

Pour déployer l'application dans l'environnement Docker, indépendemment du nombre de services décommentés, naviguez jusqu'à la racine du dossier cloné et exécutez cette commande :

```bash
docker compose up -d
```


## 5. Voir les logs des conteneurs

Pour afficher la liste des conteneurs Docker actifs :

```bash
docker ps
```

Dans la sortie, copiez l'ID du conteneur dont vous souhaitez voir les logs, puis utilisez :

```bash
docker logs {ID}
```

## 6. Arrêter et Supprimer les Conteneurs

Pour arrêter les conteneurs en cours d'exécution :

```bash
docker compose down
```

**Les données des bases de données sont persistantes ! (si vous voulez supprimer les data des bases de données, cf. la section "Nettoyer les Volumes")**


## 7. Testez votre version du microservice testé

Maintenant, il faut **valider** les modifications que vous avez apportés à la version de l'image Docker déployée avant de pouvoir lancer la CI de "release" (celle qui promeut l'image pour la prod = avec un incrément de SemVer)

Pour cela, vous allez tester les requêtes API avec Postman :

1. Ouvrir Postman
2. Paramétrer sa requête en spécifiant la route sur le nom de domaine : **http://peer.cesi/écrire/la/route/**
3. Ne pas oublier de spécifier le type de requête : **"POST" "GET" "DELETE" ...**
4. Débuggez si nécessaire (vérifier les logs du conteneurs, vérifier la route, le body de la requête, ...)
5. Valider les modifications en lançant la CI de release manuellement sur github


Si vous souhaitez tester le **front-end**, vous avez deux options.

**option 1 : accéder à l'interface web comme en prod, en passant par l'api gateway**

1. Ouvrir un navigateur
2. taper dans la barre de navigation : **"http://peer.cesi"** (il s'agit de la route paramétrée sur la gateway pour accéder à la web-app)
3. tester l'application
4. débugger
5. Valider les modifications en lançant la CI de release manuellement sur github

**option 2 : accéder à l'interface web directement sur le conteneur**

1. Ouvrir un navigateur
2. Taper dans la barre de navigation : **"http://localhost:8080"** (un port-forwarding a été configuré dans le fichier docker-compose.yaml pour connecter le port 8080 de son localhost avec le port 80 (http) du conteneur)
3. Tester l'application
4. Débugger
5. Valider les modifications en lançant la CI de release manuellement sur github

## Optionnel : Nettoyer les Volumes

Si vous souhaitez supprimer les données persistantes et repartir de zéro, n'oubliez pas de supprimer aussi les volumes :

```bash
docker volume rm $(docker volume ls -q)
```


**Attention :** Cette commande supprimera tous les volumes Docker non utilisés par des conteneurs actifs. Si vous souhaitez supprimer uniquement les volumes spécifiques à ce projet, spécifiez leurs noms explicitement.



## Optionnel : Forcer le nettoyage de tout l'environnement Docker

```bash
docker stop $(docker ps -aq) && docker rm $(docker ps -aq)
docker rmi -f $(docker images -aq)
docker volume rm $(docker volume ls -q)
docker network prune -f
```

**Attention :** Ces commandes supprimeront de manière irréversible toutes tes données dans les conteneurs, les images, les volumes, et les réseaux (à l'exception des réseaux par défaut de Docker qui ne peuvent pas être supprimés). Assure-toi d'avoir des sauvegardes ou d'être sûr de ne pas avoir besoin de ces données avant de procéder.


---

Suivez ces étapes pour configurer, déployer et gérer votre application web PeeR avec Docker Compose.
