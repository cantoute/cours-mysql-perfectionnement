# Cours MySQL Perfectionnement

## Opérateurs usuels

### SQL `LIKE`

L’opérateur LIKE est utilisé dans la clause WHERE des requêtes SQL. Ce mot-clé permet d’effectuer une recherche sur un modèle particulier. Il est par exemple possible de rechercher les enregistrements dont la valeur d’une colonne commence par telle ou telle lettre. Les modèles de recherches sont multiple.

#### Syntaxe

La syntaxe à utiliser pour utiliser l’opérateur LIKE est la suivante :

```sql
SELECT *
FROM table
WHERE colonne LIKE modele
```

Dans cet exemple le “modèle” n’a pas été défini, mais il ressemble très généralement à l’un des exemples suivants:

- `LIKE` '%a' : le caractère “%” est un caractère joker qui remplace tous les autres caractères. Ainsi, ce modèle permet de rechercher toutes les chaines de caractère qui se termine par un “a”.
- `LIKE` 'a%' : ce modèle permet de rechercher toutes les lignes de “colonne” qui commence par un “a”.
- `LIKE` '%a%' : ce modèle est utilisé pour rechercher tous les enregistrement qui utilisent le caractère “a”.
- `LIKE` 'pa%on' : ce modèle permet de rechercher les chaines qui commence par “pa” et qui se terminent par “on”, comme “pantalon” ou “pardon”.
- `LIKE` 'a_c' : le caractère “\_“ (underscore) peut être remplacé par n’importe quel caractère, mais un et un seul caractère uniquement (alors que le symbole pourcentage “%” peut être remplacé par un nombre indéterminé de caractères. Ainsi, ce modèle permet de retourner les lignes “aac”, “abc” ou même “azc”.

### SQL `IN`

L’opérateur logique IN dans SQL s’utilise avec la commande WHERE pour vérifier si une colonne est égale à une des valeurs comprise dans set de valeurs déterminés. C’est une méthode simple pour vérifier si une colonne est égale à une valeur OU une autre valeur OU une autre valeur et ainsi de suite, sans avoir à utiliser de multiple fois l’opérateur OR.

#### Syntaxe

Pour chercher toutes les lignes où la colonne “nom_colonne” est égale à ‘valeur 1’ OU ‘valeur 2’ ou ‘valeur 3’, il est possible d’utiliser la syntaxe suivante :

```sql
SELECT nom_colonne
FROM table
WHERE nom_colonne IN ( valeur1, valeur2, valeur3, ... )
```

### SQL `BETWEEN`

L’opérateur BETWEEN est utilisé dans une requête SQL pour sélectionner un intervalle de données dans une requête utilisant WHERE. L’intervalle peut être constitué de chaînes de caractères, de nombres ou de dates. L’exemple le plus concret consiste par exemple à récupérer uniquement les enregistrements entre 2 dates définies.

Syntaxe
L’utilisation de la commande BETWEEN s’effectue de la manière suivante :

```sql
SELECT *
FROM table
WHERE nom_colonne BETWEEN 'valeur1' AND 'valeur2'
```

### SQL `IS NULL` / `IS NOT NULL`

Dans le langage SQL, l’opérateur IS permet de filtrer les résultats qui contiennent la valeur NULL. Cet opérateur est indispensable car la valeur NULL est une valeur inconnue et ne peut par conséquent pas être filtrée par les opérateurs de comparaison (cf. égal, inférieur, supérieur ou différent).

Syntaxe
Pour filtrer les résultats où les champs d’une colonne sont à NULL il convient d’utiliser la syntaxe suivante:

```sql
SELECT *
FROM `table`
WHERE nom_colonne IS NULL
```

A l’inverse, pour filtrer les résultats et obtenir uniquement les enregistrements qui ne sont pas null, il convient d’utiliser la syntaxe suivante:

```sql
SELECT *
FROM `table`
WHERE nom_colonne IS NOT NULL;

-- strictement équivalent à :
SELECT *
FROM `table`
WHERE NOT nom_colonne IS NULL;
```

_A savoir :_ l’opérateur IS retourne en réalité un booléen, c’est à dire une valeur `TRUE` si la condition est vrai ou `FALSE` si la condition n’est pas respectée. Cet opérateur est souvent utilisé avec la condition WHERE mais peut aussi trouvé son utilité lorsqu’une sous-requête est utilisée.

#### Valeur `NULL`

Une colonne qui n'est pas renseignée, et donc vide, est dite contenir la valeur `NULL`. Cette valeur n'est pas zéro, c'est une absence de valeur.

Toute expression dont au moins un des termes a la valeur NULL donne comme résultat la valeur `NULL`.

Une exception à cette règle est la fonction `COALESCE`. `COALESCE` (expr1, expr2,...) renvoie la première valeur qui n'est pas null parmi les valeurs des expressions expr1, expr2,...

`COALESCE` permet de remplacer la valeur null par une autre valeur.

**Remarque importante :** les valeurs `NULL` ne sont pas inclues dans les indexes (d'autant plus intéressant dans le cas des indexes uniques).

### SQL `CASE`

Dans le langage SQL, la commande `CASE … WHEN …` permet d’utiliser des conditions de type “si / sinon” (cf. if / else) similaire à un langage de programmation pour retourner un résultat disponible entre plusieurs possibilités. Le `CASE` peut être utilisé dans n’importe quelle instruction ou clause, telle que `SELECT`, `UPDATE`, `DELETE`, `WHERE`, `ORDER BY` ou `HAVING`.

#### Syntaxe

L’utilisation du `CASE` est possible de 2 manières différentes :

Comparer une colonne à un set de résultat possible
Élaborer une série de conditions booléennes pour déterminer un résultat
Comparer une colonne à un set de résultat
Voici la syntaxe nécessaire pour comparer une colonne à un set d’enregistrement :

```sql
SELECT
  CASE t.a
    WHEN 1 THEN 'un'
    WHEN 2 THEN 'deux'
    WHEN 3 THEN 'trois'
    ELSE 'autre'
  END AS undeuxtroisautre,
  t.a
FROM
  (
    SELECT FLOOR(RAND() * 10) AS a
  ) AS t;

+------------------+---+
| undeuxtroisautre | a |
+------------------+---+
| autre            | 6 |
+------------------+---+
```

Dans cet exemple les valeurs contenus dans la colonne “a” sont comparé à 1, 2 ou 3. Si la condition est vrai, alors la valeur située après le THEN sera retournée.

A noter : la condition ELSE est facultative et sert de ramasse-miette. Si les conditions précédentes ne sont pas respectées alors ce sera la valeur du ELSE qui sera retournée par défaut.

Élaborer une série de conditions booléennes pour déterminer un résultat.

Il est possible d’établir des conditions plus complexes pour récupérer un résultat ou un autre. Cela s’effectue en utilisant la syntaxe suivante:

```sql
SELECT
  CASE
    WHEN a=b THEN 'A égal à B'
    WHEN a>b THEN 'A supérieur à B'
    ELSE 'A inférieur à B'
  END
```

Dans cet exemple les colonnes “a”, “b” et “c” peuvent contenir des valeurs numériques. Lorsqu’elles sont respectées, les conditions booléennes permettent de rentrer dans l’une ou l’autre des conditions.

Il est possible de reproduire le premier exemple présenté sur cette page en utilisant la syntaxe suivante:

```sql
CASE
  WHEN a=1 THEN 'un'
  WHEN a=2 THEN 'deux'
  WHEN a=3 THEN 'trois'
  ELSE 'autre'
END
```

#### `UPDATE` avec `CASE`

Comme cela a été expliqué au début, il est aussi possible d’utiliser le CASE à la suite de la commande SET d’un UPDATE pour mettre à jour une colonne avec une données spécifique selon une règle. Imaginons par exemple que l’ont souhaite offrir un produit pour tous les achats qui ont une surcharge inférieur à 1 et que l’ont souhaite retirer un produit pour tous les achats avec une surcharge supérieur à 1. Il est possible d’utiliser la requête SQL suivante :

```sql
UPDATE `achat`
SET `quantite` = (
  CASE
    WHEN `surcharge` < 1 THEN `quantite` + 1
    WHEN `surcharge` > 1 THEN `quantite` - 1
    ELSE quantite
  END
)
```

### SQL `LENGTH()` et utf8

**¡¡¡ Attention !!!**

```sql
SELECT LENGTH('mange');

+-----------------+
| LENGTH('mange') |
+-----------------+
|               5 |
+-----------------+

SELECT LENGTH('mangé');
+------------------+
| LENGTH('mangé')  |
+------------------+
|                6 |
+------------------+

SELECT CHAR_LENGTH('mangé');
+-----------------------+
| CHAR_LENGTH('mangé')  |
+-----------------------+
|                     5 |
+-----------------------+
```

---

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

## Les fonctions d'aggrégation (`GROUP BY`)

### `MIN()` `MAX()` `AVG()`

### `CONCAT_WS()`

### Opérateur `CASE`

## Optimisations SQL

### Les indexes

#### L'opérateur `LIKE`

### Les contraintes

### `SELECT` IN `SELECT` / `HAVING`

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
