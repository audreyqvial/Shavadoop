# SHAVADOOP  
  
**Contributeurs**: Nicolas Younes, Audrey Quessada  
    
## Objectif :   

Il s'agit de développer une version de **MapReduce** en java pour comptabiliser des mots à partir de grands fichiers `.text`.  

## Mise en oeuvre:    
* se connecter à distance à d'autres ordinateurs du réseau de l'école grâce au protocole **SSH**
* utiliser des threads pour répartir les tâches entre les différents ordinateurs **slave**, lesquels sont gérés par un **master**  
* implémenter un code java pour mettre en place une version MapReduce selon la définition de Jeff Dean:  
https://static.googleusercontent.com/media/research.google.com/fr//archive/mapreduce-osdi04.pdf
  
## Principe    
![alt text](https://github.com/audreyqvial/Shavadoop/blob/master/MapReduceProcess.jpg "principe MapReduce")  
   
## Le code  
  
Le code se divise en 2 parties: la partie **master** et la partie **slave**  
  
**Le master**   
  
Le master est lui-même subdivisé en 5 classes :  
* `Master_Shavadoop.java`    
* `Resources.java` 
* `Action.java`
* `Shuffler.java`  
* `Split_Mapper.java`  
  
Le `Master_Shavadoop.java` crée à partir de `Resources.java` une liste d'ordinateurs **slave** disponibles pour lesquels les connexions **ssh** fonctionnent. Il lance ensuite les procédures `Split_Mapper.java` et `Shuffler.java`.  
Le `Split_Mapper.java` permet de diviser un fichier par ligne ou bloc de ligne. Il crée un dictionnaire avec une clé et une liste de fichiers UMx associés à cette clé. On utilise une regex pour s'affranchir des problèmes éventuels causés par les caractères spéciaux et les pronoms. Par ailleurs, le nombre maximum de threads utilisés pour paralléliser la tâche peut être choisi pour optimiser l'execution du programme, la méthode `Join` permet de joindre les threads. Le chemin d'enregistrement des fichiers UMx peut-être modifié dans le code.    
Le `Shuffler.java` permet de distribuer de façon aléatoire vers chaque **slave** les clé et les fichiers UMx associés. Le mapping est ordonné et on récupère en sortie les fichiers SMx. On utilise la même structure que précédemment pour gérer les threads.  
Le `Action.java` lance le `SLAVE_SHAVADOOP.jar` par le biais d'un ProcessBuilder.  
  
    
**Le slave**  
  
Le slave est subdivisé en 3 classes:  
* `Slave_Shavadoop.java` 
* `Slave_Map.java` 
* `Slave_Shuffle.java` 
  
`Slave_Shavadoop.java`  permet de lancer `Slave_Map.java` puis `Slave_Shuffle.java` . `Slave_Map.java` va construire le mapping en filtrant certains mots comme les pronoms. `Slave_Shuffle.java` va executer le Reduce et créer les fichiers Rx.  
  
Pour lancer le programme, il suffit de lancer `MASTER_SHAVADOOP.jar` depuis une console ou dans un IDE de type Eclipse.  
  
## Les difficultés rencontrées

Le premier problème rencontré a été l'échec de la connexion **ssh** suite à un nombre trop important de requêtes. Nous avons donc développé un système de statuts dans la classe `Action.java` qui relance le Slave sur une nouvelle machine tant que la sortie contient un message d'erreur.  
Grâce à cela, nous pouvons donc gérer les erreurs de connexion **ssh** mais également les machines quine sont plus disponibles.  
  
Le deuxième problème rencontré a été les lenteurs dues au nombre trop important de threads créés. Nous avons donc implémenté un système permettant de mapper le nombre de threads en activité. A chaque lancement de threads, nous testons le nombre de threads actifs. Si le nombre de threads est égal au maximum defini, la relance d'un nouveau thread n'est effectuée que lorsqu'un thread a fini son action.  
Ceci permet d'éviter une trop grosse utilisation de la mémoire et donc des lenteurs, voir un arrêt du programme.  
  
## Les résultats obtenus  
### Pour le code forestier Mayotte  
Les 50 mots les plus utilisés avec les pronoms:  
  
| Mot | Compte |  
|-----|:-----:|  
| de | 12 |  
| ou | 8 |  
| biens | 8 |  
| forestier | 7 |  
| code | 6 |  
| des | 5 |  
| partie | 5 |  
| gestion | 4 |  
| aux | 4 |  
| communes | 4 |  
| le | 4 |  
| mayotte | 4 |  
| dispositions | 4 |  
| agroforestiers | 4 |  
| les | 4 |  
| a | 4 |  
| l | 4 |  
| forestiers | 4 |  
| tous | 4 |  
| legislative | 3 |  
| publiques | 2 |  
| souscrivent | 2 |  
| proprietaires | 2 |  
| livre | 2 |  
| titre | 2 |  
| attachees | 2 |  
| demembrer | 2 |  
| bonne | 2 |  
| est | 2 |  
| leur | 2 |  
| presentant | 2 |  
| l021 | 2 |  
| table | 2 |  
| fait | 2 |  
| aides | 2 |  
| dont | 2 |  
| garanties | 2 |  
| engagement | 2 |  
| preliminaire | 2 |  
| agroforestiere | 2 |  
| volontairement | 2 |  
| par | 2 |  
| regis | 2 |  
| pas | 2 |  
| que | 2 |  
| qui | 2 |  
| prioritairement | 2 |  
| unite | 2 |  
| ii | 2 |  
| accorde | 2 |   
 
Sans les pronoms:  
  
| Mot | Compte |  
|-----|:-----:|  
|biens |8|
|forestier |7|
|code |6|
|partie |5|
|gestion |4|
|dispositions |4|
|agroforestiers |4|
|communes |4|
|mayotte |4|
|forestiers |4|
|legislative |3|
|publiques |2|
|preliminaire |2|
|agroforestiere |2|
|souscrivent |2|
|volontairement |2|
|proprietaires |2|
|livre |2|
|titre |2|
|attachees |2|
|demembrer |2|
|regis |2|
|bonne |2|
|presentant |2|
|prioritairement |2|
|unite |2|
|l021 |2|
|ii |2|
|accorde |2|
|forestiere |2|
|table |2|
|propriete |2|
|fait |2|
|aides |2|
|article |2|
|constitue |2|
|garanties |2|
|benefice |2|
|engagement |2|
|plan |1|
|matieres |1|
|sommaire |1|  
  
Il existe des compteurs de mots en ligne qui permettent de vérifier le résultat sans filtrage des mots spéciaux.  
http://compteur-de-mots.net/  
  
### Pour le code de déontologie de la police nationale  
  
| Mot | Compte |  
|-----|:-----:| 
|police |38|
|nationale |20|
|article |20|
|titre |13|
|autorite |11|
|fonctionnaires |10|
|code |9|
|fonctionnaire |9|
|commandement |8|
|ordre |8|
|devoirs |7|
|deontologie |7|
|controle |6|
|execution |5|
|faire |5|
|ordres |5|
|cas |5|
|responsabilite| 4|
|doit |4|
|donne |4|
|autorites |4|
|droits |4|
|public| 4|
|personnes| 4|
|missions |4|
|personne |4|
|subordonne |4|
|fonctions |3|
|generaux |3|
|echeant |3|
|n |3|
|ier |3|
|respectifs |3|
|iii |3|
|present |3|
|preliminaire |3|
|regles |3|
|hierarchique |3|
|ii |3|
|responsable |2|
|aucune| 2|
|nature |2|
|tenu| 2|
|place |2|
|loi |2|
|actes| 2|
|conditions |2|
|executer |2|
|penale |2|
|judiciaire|2|  
  
### Pour le domaine public fluvial  
  
| Mot | Compte |  
|-----|:-----:| 
|article |175|
|bateau |106|
|immatriculation |82|
|tribunal |72|
|lieu |69|
|bureau |50|
|code |48|
|titre |48|
|navigation |46|
|bateaux |43|
|domicile |42|
|creanciers |40|
|interieure |39|
|juge |38|
|proprietaire |37|
|commerce |36|
|inscription |36|
|prix |36|
|saisie |36|
|nom |34|
|delai |34|
|inscriptions |32|
|cas |32|
|doit |32|
|date |32|
|certificat |32|
|batelier |30|
|jours |30|
|vente |30|
|greffe |28|
|acte |26|
|faite |26|
|devant |26|
|hypotheque |26|
|registre |26|
|execution |25|
|y |24|
|trouve |24|
|patron |24|
|instance |24|
|peut |24|
|livre |23|
|public |22|
|encheres |22|
|declaration |22|
|mise |22|
|conditions |21|
|navigables |21|
|voies |21|
|sans |20|  
  
Dans ce cas précis, il a fallu splitter le fichier par blocs de lignes car il est volumineux.

