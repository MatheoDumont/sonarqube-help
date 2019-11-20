# Installation et Utilisation de Sonarqube en local
  
Cette installation concerne un projet utilisant Phpunit, donc en Php.  
  

## Mettre à jour PhpUnit

Pour avoir dans Sonarqube les détails concernant la couverture des tests, il est necessaire d'ajouter 
ces lignes dans le phpunit.xml
```
<logging>
    <log type="coverage-html" target="./ci/codeCoverage/" charset="UTF-8" yui="true" highlight="false" lowUpperBound="35"     highLowerBound="70"/>
    <log type="coverage-clover" target="./ci/codeCoverage/codeCoverage.xml"/>
    <log type="junit" target="./ci/logfile.xml" logIncompleteSkipped="false"/>
</logging>
```
La premiere ligne indique le dossier dans lequel seront stockés les rapports de couverture du projet.  
Dans la deuxième, on indique qu'on veut le rendu aussi en Xml.  
La troisième indique la presence du rapport des tests unitaires dans le dossier ```ci/logfile.xml```.  

Pensez bien à créer à la racine de votre projet l'arborescence ```MonProjet/ci/codeCoverage/```.
  
## Installation, lancement et configuration de Sonarqube

### Installation
Dans un premier vous devrez télécharger [Sonarqube édition communautaire](https://www.sonarqube.org/downloads/) et l'extraire par exemple, sous Linux, dans ```/opt``` ou plus simplement dans votre repertoire personnel, ainsi vous n'aurez pas de soucis de privilegès.

Pour analyser nos projet, nous aurons aussi besoins du [Sonar-Scanner](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/) (Linux/Windows/Mac). 
Même chose que pour Sonarqube, extrayez le à l'emplacement de votre choix.
  
### Lancement
Pour être capable de scanner un projet et d'acceder aux données resultants du Scan, vous devez d'abord lancer une instance local de Sonarqube. Pour ce faire, utiliser la commande (**sans être root**) :

```
matheo  ~/Documents/manual/sonarqube-8.0  bin/linux-x86-64/sonar.sh console
```
En prenant en compte que je suis à la racine du dossier *Sonarqube* et que dans bin j'ai choisi la version pour Linux, donc choisissez bien en fonction de votre OS.

Pour pouvoir lancer le *Scanner Php* depuis la racine de vos projet, vous devrez placer 
```/sonar-scanner-4.2.0.1873-linux/bin``` dans votre PATH (en prenant toujours en compte que j'utilise la version linux).

### Configuration

L'instance de Sonarqube se trouve à l'adresse ```localhost:9000```. 
Vous devrez vous y rendre pour créer le projet de Sonarqube qui permettra de monitor votre projet correspondant.
Sonarqube vous demendera de vous connecter avant tout, vos identifiants sont :
```
login = admin
pwd = admin
```
Apres s'être connecté, vous arrivez sur une page vous proposant de créer un projet, si ce n'est pas le cas, cliquez sur le *Plus* en haut à gauche de la page.

Vous devrez ensuite donner un nom au projet, générer un token(pas utile dans ce cas), et séléctionner le langage de votre projet et votre OS.
Vous n'avez pas besoins de télécharger le *Sonar-Scanner*, comme nous l'avons déjà fait.

Pour paramétrer notre projet, il y a deux solutions soit depuis Sonarqube, soit en créant un fichier de configuration
```sonar-project.properties``` à la racine de notre projet.
C'est cette deuxième approche qui va etre utilisée.

Votre fichier à la racine devrait ressembler à ça:
```
# metadata
sonar.projectKey=VotreCleDeProjet
sonar.projectName=VotreNomDeProjet

# Dossier à analyser
sonar.sources=directory1, directory2 #...

sonar.sourceEncoding=UTF-8

sonar.php.coverage.reportPaths=./ci/codeCoverage/codeCoverage.xml
sonar.php.tests.reportPath=./ci/logfile.xml

# exemple d'exclusion de fichier/dossier
# sonar.exclusions=vendor
# Sonarqube exclut par defaut le dossier vendor pour un projet en Php
```

```VotreCleDeProjet``` et ```VotreNomDeProjet``` correspondent tous les deux au nom que vous avez specifié à la création
de votre projet sur Sonarqube, 
```sonar.php.coverage.reportPaths``` et ```sonar.php.tests.reportPath``` corresponde a la configuration ```<logging>``` donnee plus haut pour phpunit.xml 

## Analyser votre projet
Avant de lancer l'analyse, pensez bien à lancer les tests unitaires avec la configuration mise à jour :
```
./vendor/bin/phpunit
```
(sans oublier d'avoir déjà lancé votre serveur mysql).

Enfin pour lancer le Scan, **à la racine de votre projet**(sinon, il ne scannera que l'arborescence courante):
'''
sonar-scanner
'''

## Documentation et Liens utiles
Certaines infos viennent de là :  
https://geekalicious.pt/en/continuous-integration/analyse-php-laravel-5-project-multilanguage-with-sonarqube/#create-sonar-project.properties-file

Pour l'installation :  
https://docs.sonarqube.org/latest/setup/get-started-2-minutes/

Pour les clés Sonarqube dans le ```sonar-project.properties```en rapport avec les tests:  
https://docs.sonarqube.org/latest/analysis/coverage/


