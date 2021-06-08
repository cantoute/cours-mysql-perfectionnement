# Cours MySQL Perfectionnement

Ce cours est la suite du [cours d'initiation à MySQL](https://github.com/cantoute/cours-mysql-initiation).

## SQL `UNION`

La commande UNION de SQL permet de mettre bout-à-bout les résultats de plusieurs requêtes utilisant elles-même la commande SELECT. C’est donc une commande qui permet de concaténer les résultats de 2 requêtes ou plus. Pour l’utiliser il est nécessaire que chacune des requêtes à concaténer retournes le même nombre de colonnes, avec les mêmes types de données et dans le même ordre.

A savoir : par défaut, les enregistrements exactement identiques ne seront pas répétés dans les résultats. Pour effectuer une union dans laquelle même les lignes dupliquées sont affichées il faut plutôt utiliser la commande `UNION ALL`.

<picture>
  <figcaption>
    Source :
    <a href="https://sql.sh/cours/union">sql.sh/cours/jointures</a>
  </figcaption>
  <img src="images/sql-union.png" />
</picture>

```sql
SELECT * FROM table1
UNION
SELECT * FROM table2
```

```sql
SELECT 'Bob' AS prenom, 'MARLEY' AS nom
UNION
SELECT 'Tonton' AS prenom, 'DAVID' AS nom;

+--------+--------+
| prenom | nom    |
+--------+--------+
| Bob    | MARLEY |
| Tonton | DAVID  |
+--------+--------+

SELECT a.prenom
FROM (
  SELECT 'Bob' AS prenom, 'MARLEY' AS nom
  UNION
  SELECT 'Tonton' AS prenom, 'DAVID' AS nom
) AS a;

+--------+
| prenom |
+--------+
| Bob    |
| Tonton |
+--------+
```

## Jointures

### Jointures droites ou internes (`INNER JOIN`)

<picture>
  <img src="images/sql-inner-join.png" />
</picture>

```sql
SELECT *
FROM A
  INNER JOIN B ON A.key = B.key
```

Notons que c'est strictement équivalent à :

```sql
SELECT *
FROM A, B
WHERE A.key = B.key
```

### Jointures externes `[LEFT|RIGHT|FULL] OUTER JOIN`

#### `LEFT JOIN`

<picture>
  <img src="images/sql-left-join.png" />
</picture>

```sql
SELECT *
FROM A
  LEFT JOIN B ON A.key = B.key
```

#### `LEFT JOIN` (sans l’intersection de B)

Autrement dit, c'est ainsi que l'on rechercher les veufs et orphelins.

<picture>
  <img src="images/sql-left-join-exclude.png" />
</picture>

```sql
SELECT *
FROM A
  LEFT JOIN B ON A.key = B.key
WHERE B.key IS NULL
```

#### `RIGHT JOIN`

<picture>
  <img src="images/sql-right-join.png" />
</picture>

```sql
SELECT *
FROM A
  RIGHT JOIN B ON A.key = B.key
```

#### `RIGHT JOIN` (sans l’intersection de B)

<picture>
  <img src="images/sql-right-join-exclude.png" />
</picture>

```sql
SELECT *
FROM A
  RIGHT JOIN B ON A.key = B.key
WHERE B.key IS NULL
```

#### `FULL JOIN`

<picture>
  <img src="images/sql-full-join.png" />
</picture>

```sql
SELECT *
FROM A
  FULL JOIN B ON A.key = B.key
```

#### `FULL JOIN` (sans intersection)

<picture>
  <img src="images/sql-full-join-exclude.png" />
</picture>

```sql
SELECT *
FROM A
  FULL JOIN B ON A.key = B.key
WHERE
  A.key IS NULL
  OR B.key IS NULL
```

### `CROSS JOIN` (Jointures croisées)

Dans le langage SQL, la commande CROSS JOIN est un type de jointure sur 2 tables SQL qui permet de retourner le produit cartésien. Autrement dit, cela permet de retourner chaque ligne d’une table avec chaque ligne d’une autre table. Ainsi effectuer le produit cartésien d’une table A qui contient 30 résultats avec une table B de 40 résultats va produire 1200 résultats (30 x 40 = 1200). En général la commande CROSS JOIN est combinée avec la commande WHERE pour filtrer les résultats qui respectent certaines conditions.

Attention, le nombre de résultat peut facilement être très élevé. S’il est effectué sur des tables avec beaucoup d’enregistrements, cela peut ralentir sensiblement le serveur.

#### Syntaxe

Pour effectuer un jointure avec CROSS JOIN, il convient d’effectuer une requête SQL respectant la syntaxe suivante:

```sql
SELECT *
FROM table1
  CROSS JOIN table2
```

_Méthode alternative pour retourner les mêmes résultats :_

```sql
SELECT *
FROM table1, table2
```

### SQL `EXISTS`

Dans le langage SQL, la commande EXISTS s’utilise dans une clause conditionnelle pour savoir s’il y a une présence ou non de lignes lors de l’utilisation d’une sous-requête.

A noter : cette commande n’est pas à confondre avec la clause IN. La commande EXISTS vérifie si la sous-requête retourne un résultat ou non, tandis que IN vérifie la concordance d’une à plusieurs données.

#### Syntaxe

L’utilisation basique de la commande EXISTS consiste à vérifier si une sous-requête retourne un résultat ou non, en utilisant EXISTS dans la clause conditionnelle. La requête externe s’exécutera uniquement si la requête interne retourne au moins un résultat.

```sql
SELECT nom_colonne1
FROM `table1`
WHERE EXISTS (
    SELECT nom_colonne2
    FROM `table2`
    WHERE nom_colonne3 = 10
  )
```

Dans l’exemple ci-dessus, s’il y a au moins une ligne dans table2 dont nom_colonne3 contient la valeur 10, alors la sous-requête retournera au moins un résultat. Dès lors, la condition sera vérifiée et la requête principale retournera les résultats de la colonne nom_colonne1 de table1.

#### Exemple

Dans le but de montrer un exemple concret d’application, imaginons un système composé d’une table qui contient des commandes et d’une table contenant des produits.

##### Table commande :

| c_id | c_date_achat | c_produit_id | c_quantite_produit |
| ---- | ------------ | ------------ | ------------------ |
| 1    | 2014-01-08   | 2            | 1                  |
| 2    | 2014-01-24   | 3            | 2                  |
| 3    | 2014-02-14   | 8            | 1                  |
| 4    | 2014-03-23   | 10           | 1                  |

##### Table produit :

| p_id | p_nom      | p_date_ajout | p_prix |
| ---- | ---------- | ------------ | ------ |
| 2    | Ordinateur | 2013-11-17   | 799.9  |
| 3    | Clavier    | 2013-11-27   | 49.9   |
| 4    | Souris     | 2013-12-04   | 15     |
| 5    | Ecran      | 2013-12-15   | 250    |

Il est possible d’effectuer une requête SQL qui affiche les commandes pour lesquels il y a effectivement un produit. Cette requête peut être interprétée de la façon suivante :

```sql
SELECT *
FROM commande
WHERE EXISTS (
  SELECT _
  FROM produit
  WHERE c_produit_id = p_id
)
```

Résultat :

| c_id | c_date_achat | c_produit_id | c_quantite_produit |
| ---- | ------------ | ------------ | ------------------ |
| 1    | 2014-01-08   | 2            | 1                  |
| 2    | 2014-01-24   | 3            | 2                  |

Le résultat démontre bien que seul les commandes n°1 et n°2 ont un produit qui se trouve dans la table produit (cf. la condition c_produit_id = p_id). Cette requête est intéressante sachant qu’elle n’influence pas le résultat de la requête principale, contrairement à l’utilisation d’une jointure qui va concaténer les colonnes des 2 tables jointes.

### SQL `DISTINCT` (Éviter les doublons)

L’utilisation de la commande SELECT en SQL permet de lire toutes les données d’une ou plusieurs colonnes. Cette commande peut potentiellement afficher des lignes en doubles. Pour éviter des redondances dans les résultats il faut simplement ajouter DISTINCT après le mot SELECT.

#### Commande basique

L’utilisation basique de cette commande consiste alors à effectuer la requête suivante:

```sql
SELECT DISTINCT ma_colonne
FROM nom_du_tableau
```

Cette requête sélectionne le champ “ma_colonne” de la table “nom_du_tableau” en évitant de retourner des doublons.

### Éviter les paires inverses

## Optimisations SQL

### Les indexes

#### Céf primaire (_Primary Key_)

Une clé primaire est unique et ne peut jamais être nulle. Il identifiera toujours un seul enregistrement, et chaque enregistrement doit être représenté. Une table ne peut avoir qu'une seule clé primaire.

```sql
ALTER TABLE Employees ADD PRIMARY KEY(ID);
```

### Unique Index

Un index unique doit être unique, mais il peut être nul. Ainsi, chaque valeur de clé n'identifie qu'un seul enregistrement, mais chaque enregistrement n'a pas besoin d'être représenté.

For example, to create a unique key on the Employee_Code field, as well as a primary key, use:

```sql
CREATE TABLE `Employees` (
  `ID` TINYINT(3) UNSIGNED NOT NULL,
  `First_Name` VARCHAR(25) NOT NULL,
  `Last_Name` VARCHAR(25) NOT NULL,
  `Position` VARCHAR(25) NOT NULL,
  `Home_Address` VARCHAR(50) NOT NULL,
  `Home_Phone` VARCHAR(12) NOT NULL,
  `Employee_Code` VARCHAR(25) NOT NULL,
  PRIMARY KEY (`ID`),
  UNIQUE KEY (`Employee_Code`)
);
```

Les indexes peuvent également être ajoutés ultérieurement :

```sql
ALTER TABLE Employees ADD UNIQUE `EmpCode`(`Employee_Code`);

-- équivalent
CREATE UNIQUE INDEX HomePhone ON Employees(Home_Phone);
```

Les index peuvent impliquer plusieurs colonnes. MariaDB est capable d'utiliser une ou plusieurs colonnes sur la partie la plus à gauche de l'index si elle ne peut pas utiliser l'index entier.

Exemple :

```sql
CREATE TABLE t1 (a INT NOT NULL, b INT, UNIQUE (a,b));

INSERT INTO t1 values (1,1), (2,2);

SELECT * FROM t1;
+---+------+
| a | b    |
+---+------+
| 1 |    1 |
| 2 |    2 |
+---+------+
```

Étant donné que l'index est défini comme unique sur les deux colonnes a et b, la ligne suivante est valide, car si ni a ni b ne sont uniques en eux-mêmes, la combinaison est unique :

```sql
INSERT INTO t1 values (2,1);

SELECT * FROM t1;
+---+------+
| a | b    |
+---+------+
| 1 |    1 |
| 2 |    1 |
| 2 |    2 |
+---+------+
```

Le fait qu'une contrainte UNIQUE puisse être NULL est souvent négligé. En SQL, tout NULL n'est jamais égal à quoi que ce soit, même pas à un autre NULL. Par conséquent, une contrainte UNIQUE n'empêchera pas de stocker des lignes en double si elles contiennent des valeurs nulles :

```sql
INSERT INTO t1 values (3,NULL), (3, NULL);

SELECT * FROM t1;
+---+------+
| a | b    |
+---+------+
| 1 |    1 |
| 2 |    1 |
| 2 |    2 |
| 3 | NULL |
| 3 | NULL |
+---+------+
```

En effet, en SQL les deux dernières lignes, même identiques, ne sont pas égales :

```sql
SELECT (3, NULL) = (3, NULL);

+-----------------------+
| (3, NULL) = (3, NULL) |
+-----------------------+
|                  NULL |
+-----------------------+
```

Dans MariaDB, vous pouvez combiner cela avec des colonnes virtuelles pour appliquer l'unicité sur un sous-ensemble de lignes dans une table :

```sql
create table Table_1 (
  user_name varchar(10),
  status enum('Active', 'On-Hold', 'Deleted'),
  del char(0) as (if(status in ('Active', 'On-Hold'),'', NULL)) persistent,
  unique(user_name,del)
)
```

Cette structure de table garantit que tous les utilisateurs actifs ou en attente ont des noms distincts, mais dès qu'un utilisateur est supprimé, son nom ne fait plus partie de la contrainte d'unicité et un autre utilisateur peut obtenir le même nom.

Si un index unique consiste en une colonne dans laquelle les caractères de fin de remplissage sont supprimés ou ignorés, les insertions dans cette colonne où les valeurs diffèrent uniquement par le nombre de caractères de fin de remplissage entraîneront une erreur de clé en double.

#### L'opérateur `LIKE`

L'opérteur LIKE ne peut s'appuyer sur les indexes uniquement dans pour les cas de recherche de chaines commençant par.

```sql
-- s'il existe un index, il pourra être utilisé
SELECT username
FROM student
WHERE last_name LIME 'g%';

-- l'index ne pourra être utilisé
SELECT username
FROM student
WHERE last_name LIME '%g';

```

### Les contraintes

MariaDB prend en charge l'implémentation de contraintes au niveau de la table à l'aide des instructions CREATE TABLE ou ALTER TABLE. Une contrainte de table restreint les données que vous pouvez ajouter à la table. Si vous essayez d'insérer des données non valides dans une colonne, MariaDB renvoie une erreur.

#### Syntaxe

```
[CONSTRAINT [symbol]] constraint_expression

constraint_expression:
  | PRIMARY KEY [index_type] (index_col_name, ...) [index_option] ...
  | FOREIGN KEY [index_name] (index_col_name, ...)
       REFERENCES tbl_name (index_col_name, ...)
       [ON DELETE reference_option]
       [ON UPDATE reference_option]
  | UNIQUE [INDEX|KEY] [index_name]
       [index_type] (index_col_name, ...) [index_option] ...
  | CHECK (check_constraints)

index_type:
  USING {BTREE | HASH | RTREE}

index_col_name:
  col_name [(length)] [ASC | DESC]

index_option:
  | KEY_BLOCK_SIZE [=] value
  | index_type
  | WITH PARSER parser_name
  | COMMENT 'string'
  | CLUSTERING={YES|NO}

reference_option:
  RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT
```

Description
Constraints provide restrictions on the data you can add to a table. This allows you to enforce data integrity from MariaDB, rather than through application logic. When a statement violates a constraint, MariaDB throws an error.

There are four types of table constraints:

| Constraint  | Description                                                               |
| ----------- | ------------------------------------------------------------------------- |
| PRIMARY KEY | Sets the column for referencing rows. Values must be unique and not null. |
| FOREIGN KEY | Sets the column to reference the primary key on another table.            |
| UNIQUE      | Requires values in column or columns only occur once in the table.        |
| CHECK       | Checks whether the data meets the given condition.                        |

La table [Information Schema TABLE_CONSTRAINTS](https://mariadb.com/kb/en/information-schema-table_constraints-table/) contient des informations sur les tables qui ont des contraintes.

#### FOREIGN KEY Constraints

InnoDB supports foreign key constraints. The syntax for a foreign key constraint definition in InnoDB looks like this:

```
[CONSTRAINT [symbol]] FOREIGN KEY
    [index_name] (index_col_name, ...)
    REFERENCES tbl_name (index_col_name,...)
    [ON DELETE reference_option]
    [ON UPDATE reference_option]

reference_option:
    RESTRICT | CASCADE | SET NULL | NO ACTION
```

#### CHECK Constraints

> MariaDB à partir de 10.2.1
>
> A partir de MariaDB 10.2.1, les contraintes sont appliquées. Avant MariaDB 10.2.1, les expressions de contrainte étaient acceptées dans la syntaxe mais ignorées.

Dans MariaDB 10.2.1, vous pouvez définir des contraintes de 2 manières différentes :

- `CHECK(expression)` comme argument de définition de colonne.
- `CONSTRAINT [constraint_name] CHECK (expression)`

Before a row is inserted or updated, all constraints are evaluated in the order they are defined. If any constraint expression returns false, then the row will not be inserted or updated. One can use most deterministic functions in a constraint, including UDFs.

```sql
CREATE TABLE t1 (a INT CHECK (a>2), b INT CHECK (b>2), CONSTRAINT a_greater CHECK (a>b));
```

Si vous utilisez le second format et que vous ne donnez pas de nom à la contrainte, alors la contrainte recevra un nom généré automatiquement. Ceci est fait pour que vous puissiez ultérieurement supprimer la contrainte avec ALTER TABLE DROP nom_contrainte.

On peut désactiver toutes les vérifications d'expression de contrainte en définissant la variable check_constraint_checks sur OFF. Ceci est utile par exemple lors du chargement d'une table qui viole certaines contraintes que vous souhaitez rechercher et corriger ultérieurement dans SQL.

#### Auto_increment

> MariaDB starting with 10.2.6
>
> From MariaDB 10.2.6, auto_increment columns are no longer permitted in check constraints. Previously they were permitted, but would not work correctly. See [MDEV-11117](https://jira.mariadb.org/browse/MDEV-11117).

#### Examples

```sql
CREATE TABLE product (
  category INT NOT NULL,
  id INT NOT NULL,
  price DECIMAL,
  PRIMARY KEY(category, id)) ENGINE=INNODB;

CREATE TABLE customer (
  id INT NOT NULL,
  PRIMARY KEY (id)) ENGINE=INNODB;

CREATE TABLE product_order (
  no INT NOT NULL AUTO_INCREMENT,
  product_category INT NOT NULL,
  product_id INT NOT NULL,
  customer_id INT NOT NULL,
  PRIMARY KEY(no),
  INDEX (product_category, product_id),
  FOREIGN KEY (product_category, product_id)
    REFERENCES product(category, id)
    ON UPDATE CASCADE ON DELETE RESTRICT,
  INDEX (customer_id),
  FOREIGN KEY (customer_id)
    REFERENCES customer(id)) ENGINE=INNODB;
```

> MariaDB starting with 10.2.1
>
> The following examples will work from MariaDB 10.2.1 onwards.

Numeric constraints and comparisons:

```sql
CREATE TABLE t1 (a INT CHECK (a>2), b INT CHECK (b>2), CONSTRAINT a_greater CHECK (a>b));

INSERT INTO t1(a) VALUES (1);
ERROR 4022 (23000): CONSTRAINT `a` failed for `test`.`t1`

INSERT INTO t1(a,b) VALUES (3,4);
ERROR 4022 (23000): CONSTRAINT `a_greater` failed for `test`.`t1`

INSERT INTO t1(a,b) VALUES (4,3);
Query OK, 1 row affected (0.04 sec)
```

Dropping a constraint:

```sql
ALTER TABLE t1 DROP CONSTRAINT a_greater;
```

```sql
Adding a constraint:

ALTER TABLE t1 ADD CONSTRAINT a_greater CHECK (a>b);
Date comparisons and character length:

CREATE TABLE t2 (
  name VARCHAR(30) CHECK (CHAR_LENGTH(name)>2),
  start_date DATE,
  end_date DATE CHECK (
    start_date IS NULL
    OR end_date IS NULL
    OR start_date<end_date
  )
);

INSERT INTO t2(name, start_date, end_date) VALUES('Ione', '2003-12-15', '2014-11-09');
Query OK, 1 row affected (0.04 sec)

INSERT INTO t2(name, start_date, end_date) VALUES('Io', '2003-12-15', '2014-11-09');
ERROR 4022 (23000): CONSTRAINT `name` failed for `test`.`t2`

INSERT INTO t2(name, start_date, end_date) VALUES('Ione', NULL, '2014-11-09');
Query OK, 1 row affected (0.04 sec)

INSERT INTO t2(name, start_date, end_date) VALUES('Ione', '2015-12-15', '2014-11-09');
ERROR 4022 (23000): CONSTRAINT `end_date` failed for `test`.`t2`
```

A misplaced parenthesis:

```sql
CREATE TABLE t3 (name VARCHAR(30) CHECK (CHAR_LENGTH(name>2)), start_date DATE,
  end_date DATE CHECK (start_date IS NULL OR end_date IS NULL OR start_date<end_date));
Query OK, 0 rows affected (0.32 sec)

INSERT INTO t3(name, start_date, end_date) VALUES('Io', '2003-12-15', '2014-11-09');
Query OK, 1 row affected, 1 warning (0.04 sec)

SHOW WARNINGS;
+---------+------+----------------------------------------+
| Level   | Code | Message                                |
+---------+------+----------------------------------------+
| Warning | 1292 | Truncated incorrect DOUBLE value: 'Io' |
+---------+------+----------------------------------------+
```

Compare the definition of table t2 to table t3. CHAR_LENGTH(name)>2 is very different to CHAR_LENGTH(name>2) as the latter mistakenly performs a numeric comparison on the name field, leading to unexpected results.

---

### `SELECT` IN `SELECT` / `HAVING` / `EXISTS`

Dans ces cas de figure, l'optimiseur de requête ne pourra pas utiliser les indexes.

Souvent ces écritures permettent de simplifier l'écriture mais aussi la lecture/comprehension de la requête mais s'il vous fallait améliorer les performances de la requête et que vous avez le choix d'utiliser une liaison de type `JOIN` vous obtiendrez un gain de performance significatif.

#### ne peut s'appuyer sur les indexes

```sql
SELECT id
FROM subsource_position
WHERE
  id NOT IN (SELECT position_id FROM subsource)
```

#### peut utiliser les indexes de façon optimale

```sql
SELECT id FROM subsource_position
EXCEPT
SELECT position_id FROM subsource;
```

```sql
SELECT sp.id
FROM subsource_position AS sp
  LEFT JOIN subsource AS s ON (s.postion_id = sp.id)
WHERE
  s.postion_id IS NULL
```

### Analyse d'execution (`EXPLAIN`)

### Fonctionnement du cache MySQL

#### Désactiver le cache pour une requête

## Vues (`VIEW`)

## Déclencheurs (`TRIGGER`)

## Les transactions (`BEGIN`, `COMMIT`, `ROLLBACK`)

## Optimisations serveur

### Quelques recommendations

Ne vous fiez pas trop à ce qui se raconte, ni même aux conseils donnés dans la documentation. En fait on a souvent de meilleurs performances en faisant l'inverse de ce qu'ils recommandent.

- Ne pas désactiver le cache!
- Quand ils recommandent un chiffre grand, essayez un petit, et vice et versa.

Par expérience, MySQL à tendance à manger la RAM à mesure qu'il tourne puis finit par se stabiliser.
J'ai tout essayé... Il bouffe la RAM quoi qu'on fasse.

### Utilisation d'utilitaires d'aides à la configuration.

#### PhpMyAdmin

Via `État > Conseiller`

Certains paramètres du serveur peuvent être ajustés à chaud via SQL ou par le menu `Variables` de PhpMyAdmin. Il faut que vous ayez les droits nécessaires mais certains paramètres ne peuvent être ajusté autrement que par les fichiers de configuration.

Notez bien que les paramètres saisis en SQL ou par phpMyAdmin seront perdu au redémarrage de MySQL. Mais c'est pratique de passer par là pour faire des tests sans avoir à redémarrer MySQL à chaque essai.

#### `tuning-primer.sh`

Cela vous nécessitera un accès à une console (terminal/ssh)

Le script peut être téléchargé ici
https://github.com/BMDan/tuning-primer.sh

### Exemple de configuration

Cette configuration est appliquée sur un serveur hébergeant ~80 WordPress avec 8 cœurs et 16Go. de RAM.

```ini
# devrait être un paramètre par défaut tant cela améliore grandement les performances

skip-name-resolve

max_connections         = 150

tmp_table_size          = 64M
max_heap_table_size     = 64M
read_rnd_buffer_size    = 4M

innodb_log_file_size    = 64M

join_buffer_size        = 2M

innodb_buffer_pool_size = 512M

key_buffer_size         = 16M
max_allowed_packet      = 64M
thread_stack            = 192K
thread_cache_size       = 8

table_open_cache        = 8000

#
# * Query Cache Configuration
#
query_cache_limit       = 2M
query_cache_size        = 128M


# Sécurité
# ne pas ouvrir l'accès direct depuis les interfaces extérieures
bind-address            = 127.0.0.1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam_recover_options  = BACKUP

```

### Utilisation d'un dossier temporaire en RAM (RAMFS/TMPFS)

Quand les requêtes sont un peut complexes, MySQL a besoin de coucher des étapes intermédiaires sur disque. Par défaut cela se passe dans le dossier temporaire. (Sous \*nix /tmp)

Pour améliorer les performances on peut créer un dosser temporaire avec un système de fichiers en mémoire (RAMFS tout TMPFS).

Premièrement déclarer un système de fichier TMPFS sur /tmp2

`/etc/fstab`

```ini
# <file system> <mount point>   <type>  <options>       <dump>  <pass>

tmpfs           /tmp2   tmpfs   defaults,size=2g    0       0

# default signifie de le charger au boot du système

```

La première fois, il faudra monter /tmp2 manuellement

```bash
sudo mount /tmp2
```

Enfin indiquer à MySQL (ou MariaDB) d'utiliser ce dossier. Trouvez la ligne indiquant le paramètre tmpdir :

`/etc/mysql/mariadb.conf.d/50-server.cnf`

```ini
tmpdir = /tmp2
```

<style>
img {
  display: block;
  margin: 1em auto;
}
</style>
