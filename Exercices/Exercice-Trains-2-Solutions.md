# Exercice Trains 2 - Solutions

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

<img src="Exercice-Trains-2-Solutions.png" style="display:block; margin: 1em auto;" />

```sql

DROP TABLE IF EXISTS arret;
CREATE TABLE arret (
  id int(11) NOT NULL,
  ligne_id int(11) NOT NULL,
  rang int(11) NOT NULL,
  ville varchar(255) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS ligne;
CREATE TABLE ligne (
  id int(11) NOT NULL,
  ville_dep varchar(255) NOT NULL,
  ville_arr varchar(255) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS trafic;
CREATE TABLE trafic (
  id int(11) NOT NULL,
  ligne_id int(11) NOT NULL,
  jour int(11) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS train;
CREATE TABLE train (
  id int(11) NOT NULL,
  trafic_id int(11) NOT NULL,
  wagon_id int(11) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS wagon;
CREATE TABLE wagon (
  id int(11) NOT NULL,
  type_wagon enum('Passagers','Marchandise') NOT NULL,
  poids_vide int(11) DEFAULT NULL,
  capacite int(11) DEFAULT NULL,
  etat enum('Libre','Occupé') NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;


ALTER TABLE arret
  ADD PRIMARY KEY (id),
  ADD KEY ligne_id (ligne_id);

ALTER TABLE ligne
  ADD PRIMARY KEY (id);

ALTER TABLE trafic
  ADD PRIMARY KEY (id),
  ADD KEY ligne_id (ligne_id);

ALTER TABLE train
  ADD PRIMARY KEY (id),
  ADD UNIQUE KEY trafic_id (trafic_id,wagon_id),
  ADD KEY wagon_id (wagon_id);

ALTER TABLE wagon
  ADD PRIMARY KEY (id);


ALTER TABLE arret
  MODIFY id int(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE ligne
  MODIFY id int(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE trafic
  MODIFY id int(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE train
  MODIFY id int(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE wagon
  MODIFY id int(11) NOT NULL AUTO_INCREMENT;


ALTER TABLE arret
  ADD CONSTRAINT arret_ibfk_1 FOREIGN KEY (ligne_id) REFERENCES ligne (id) ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE trafic
  ADD CONSTRAINT trafic_ibfk_1 FOREIGN KEY (ligne_id) REFERENCES ligne (id) ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE train
  ADD CONSTRAINT train_ibfk_1 FOREIGN KEY (trafic_id) REFERENCES trafic (id) ON DELETE CASCADE ON UPDATE CASCADE,
  ADD CONSTRAINT train_ibfk_2 FOREIGN KEY (wagon_id) REFERENCES wagon (id) ON DELETE CASCADE ON UPDATE CASCADE;

```

## Donner les requêtes SQL permettant de répondre aux questions suivantes :

1. Donner la liste des gares traversées par la ligne 3. Le résultat sera renommé ’GareTraverseeParL3’ et sera trié selon le rang de l’arrêt.

2. Donner les numéros des wagons faisant à vide entre 800kg et 2tonnes et de type ’Marchandise’.

3. Donner les wagons dont on ne connaît pas soit le poids à vide soit la capacité.

4. Donner les numéros des lignes partant de Lyon et pour lesquelles au moins un train circule le mercredi.

5. Donner les wagons de type ’Marchandise’ de capacité inférieure strictement à celle du wagon 12.

6. Donner les couples de numéros de wagons qui sont libres, de même type, de même poids à vide mais de capacités différentes.

7. Donner le numéro des trains qui sont composés d’au moins deux wagons de type ’Passagers’ et qui partent de ’Dijon’ ou arrivent à ’Valence’.
