# Exercice Trains 2

## On considère le schéma de base de données suivant :

```
Ligne (NoLigne, VilleDep, VilleArr)
Arret (#NoLigne, Rang, Ville)
Trafic (NoTrain, #NoLigne, NoJour)
Train (#NoTrain, #NoWagon)
Wagon (NoWagon, TypeWagon, PoidsVide, Capacite, Etat)
```

- La relation Ligne donne pour chaque ligne de train la gare de départ et la gare d’arrivée.
- La relation Arret donne le rang et la ville de l’arrêt pour une ligne donnée.
- La relation Trafic associe les trains et les lignes parcourues par ces trains à un numéro de jour de semaine donné (1 pour lundi, 2 pour mardi...). La relation Train liste les wagons qui composent un train.
- La relation Wagon donne les caractéristiques de chaque wagon (l’état peut être "libre" ou "occupé"). Le poids est exprimé en kg.

## Exemple d’instance de base :

- Pour Ligne : {(1,’Lyon’,’Clermont-Ferrand’), (2,’Lyon’, ’Marseille’), ... }
- Pour Arret : {(1,1,’Gannat’), (2,1,’Valence’), (2,2, ’Avignon’), ... }
- Pour Trafic : {(1,1,1),(1,1,2),(1,1,3),(1,1,4), (1,1,5),(2,2,6),(2,2,7), ... }
- Pour Train : {(1,1), (1,3), (1,4), (1,5), (2,9), (2,10), ... }
- Pour Wagon : {(1,’Passagers’, NULL, 150, ’Libre’ ),
  (2,’Marchandise’, 12345, NULL, ’Occupé’ ), ... }

## Donner les requêtes SQL permettant de répondre aux questions suivantes :

1. Donner la liste des gares traversées par la ligne 3. Le résultat sera renommé ’GareTraverseeParL3’ et sera trié selon le rang de l’arrêt.

2. Donner les numéros des wagons faisant à vide entre 800kg et 2tonnes et de type ’Marchandise’.

3. Donner les wagons dont on ne connaît pas soit le poids à vide soit la capacité.

4. Donner les numéros des lignes partant de Lyon et pour lesquelles au moins un train circule le mercredi.

5. Donner les wagons de type ’Marchandise’ de capacité inférieure strictement à celle du wagon 12.

6. Donner les couples de numéros de wagons qui sont libres, de même type, de même poids à vide mais de capacités différentes.

7. Donner le numéro des trains qui sont composés d’au moins deux wagons de type ’Passagers’ et qui partent de ’Dijon’ ou arrivent à ’Valence’.
