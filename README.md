# Implémentation d'un Serveur HTTP en Java

Une implémentation légère d'un serveur HTTP en Java avec des capacités de filtrage IP et d'exécution de scripts intégrés. Ce serveur est conçu comme un projet éducatif pour démontrer les concepts fondamentaux d'un serveur web.

## Fonctionnalités

- **Serveur HTTP basique :** Gère les requêtes HTTP GET et sert des fichiers depuis un répertoire configuré
- **Filtrage IP :** Contrôle l'accès avec des règles d'acceptation/rejet d'adresses IP
- **Journalisation des accès :** Suivi de tous les événements d'accès et d'erreur du serveur
- **Exécution de scripts intégrés :** Exécution de scripts (bash, python) directement depuis HTML à l'aide des balises `<code>`
- **Configuration simple :** Fichier de configuration basé sur XML
- **Page de statut :** Rapports dynamiques sur l'état du système
- **Documentation UML :** Documentation complète de l'architecture avec des diagrammes PlantUML

## Table des matières

- [Architecture](#architecture)
- [Installation](#installation)
- [Configuration](#configuration)
- [Utilisation](#utilisation)
- [Exemples](#exemples)
- [Structure du projet](#structure-du-projet)
- [Diagrammes UML](#diagrammes-uml)
- [Contributeurs](#contributeurs)

## Architecture

Le serveur se compose de plusieurs composants clés :

- **HttpServer** : Classe principale du serveur qui initialise le socket serveur et délègue les requêtes des clients
- **DemandeConnection** : Gère les connexions des clients, vérifie les adresses IP et traite les requêtes HTTP
- **GestionDesAccess** : Gère les journaux d'accès pour les connexions et requêtes acceptées
- **GestionDesErrors** : Gère les journaux d'erreurs pour les connexions rejetées et les erreurs
- **VerificateurAdresseIP** : Classe utilitaire pour vérifier si une adresse IP appartient à un réseau spécifique

Le serveur suit un processus simple de traitement des requêtes :
1. Accepter la connexion entrante
2. Valider l'IP du client par rapport aux réseaux autorisés/refusés
3. Analyser la requête HTTP
4. Servir le fichier demandé ou renvoyer une erreur
5. Journaliser la transaction

## Installation

### Prérequis

- Java JDK 11 ou supérieur
- Structure de répertoires correctement configurée

### Configuration

1. Cloner le dépôt :
   ```bash
   git clone https://github.com/korban2u/Serveur_Web_Configuration.git
   cd myweb
   ```

2. Compiler les classes Java :
   ```bash
   javac -d . usr/local/sbin/myweb/*.java
   ```

3. Créer les répertoires nécessaires :
   ```bash
   mkdir -p var/log/myweb
   mkdir -p var/www
   mkdir -p etc/myweb
   ```

4. Configurer le serveur (voir [Configuration](#configuration))

5. Rendre les scripts de démarrage/arrêt exécutables :
   ```bash
   chmod +x start.sh finish.sh
   ```

## Configuration

Le serveur est configuré à l'aide d'un fichier XML situé à `etc/myweb/myweb.conf` :

```xml
<webconf>
    <port>8080</port>
    <root>var/www</root>
    <accept>0:0:0:0:0:0:0:1/8</accept>
    <reject>192.168.0.0/24</reject>
    <accesslog>var/log/myweb/acces.log</accesslog>
    <errorlog>var/log/myweb/errors.log</errorlog>
</webconf>
```

Options de configuration :
- **port** : Port sur lequel le serveur écoutera
- **root** : Répertoire racine des fichiers web
- **accept** : Liste d'adresses IP/réseaux à accepter, séparés par des virgules
- **reject** : Liste d'adresses IP/réseaux à rejeter, séparés par des virgules
- **accesslog** : Chemin vers le fichier journal d'accès
- **errorlog** : Chemin vers le fichier journal d'erreurs

## Utilisation

### Démarrage du serveur

```bash
./start.sh
```

Cela démarrera le serveur en arrière-plan et sauvegardera l'ID de processus dans `/run/myweb.pid`.

### Arrêt du serveur

```bash
./finish.sh
```

Cela arrêtera le serveur en terminant le processus avec l'ID stocké dans `/run/myweb.pid`.

### Accès au serveur Web

Ouvrez un navigateur web et naviguez vers :
```
http://localhost:8080/
```

Remplacez `localhost` par le nom d'hôte du serveur et `8080` par le port configuré.

## Exemples

### Page HTML basique

Créez un fichier HTML dans le répertoire `var/www` :

```html
<html>
    <head>
        <title>Ma page Web</title>
    </head>
    <body>
        <h1>Bonjour, Monde !</h1>
    </body>
</html>
```

### Exécution de script intégré

Créez un fichier HTML avec des scripts intégrés :

```html
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h1>Date et heure actuelles</h1>
        <h2>Utilisation de Bash</h2>
        <code interpreteur="/bin/bash">
date
        </code>
        
        <h2>Utilisation de Python</h2>
        <code interpreteur="/usr/bin/python3">
import datetime
print(datetime.datetime.now())
        </code>
    </body>
</html>
```

### Page de statut du système

Le serveur inclut une page de statut intégrée à `/status.html` qui affiche :
- Mémoire RAM disponible
- Espace disque disponible
- Nombre de processus en cours d'exécution

## Structure du projet

```
├── DIAG/                           # Diagrammes UML
│   ├── Gestion_requete_client.puml
│   ├── Initialisation_et_demarrage_serveur.puml
│   └── myweb.plantuml
├── etc/
│   └── myweb/
│       └── myweb.conf              # Configuration du serveur
├── usr/
│   └── local/
│       └── sbin/
│           └── myweb/              # Fichiers source Java
│               ├── DemandeConnection.java
│               ├── GestionDesAccess.java
│               ├── GestionDesErrors.java
│               ├── HttpServer.java
│               └── VerificateurAdresseIP.java
├── var/
│   ├── log/
│   │   └── myweb/                  # Fichiers de journalisation
│   │       ├── acces.log
│   │       └── errors.log
│   └── www/                        # Contenu web
│       ├── index.html
│       ├── miniWeb.html
│       ├── siteweb.html
│       ├── status.html
│       └── status.sh
├── start.sh                        # Script de démarrage du serveur
└── finish.sh                       # Script d'arrêt du serveur
```

## Diagrammes UML

Le projet inclut des diagrammes UML qui documentent l'architecture du système :

1. **Diagramme de classes** (`DIAG/myweb.plantuml`) : Montre les relations entre les principales classes
2. **Diagramme de séquence - Initialisation du serveur** (`DIAG/Initialisation_et_demarrage_serveur.puml`) : Illustre le processus de démarrage du serveur
3. **Diagramme de séquence - Traitement des requêtes** (`DIAG/Gestion_requete_client.puml`) : Décrit comment les requêtes des clients sont traitées

Pour visualiser ces diagrammes, utilisez un visualiseur PlantUML ou générez des images avec l'outil PlantUML.

## Contributeurs

- Aloïs Masson-Claudez
- Benjamin Scheffer
- Korban Ryan

---

*Ce projet a été créé comme un exercice éducatif pour démontrer les concepts de serveur web en Java.*
