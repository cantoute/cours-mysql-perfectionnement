# Solutions - Exercice les Artistes

Le travail est à réaliser à partir du fichier disponible à l’adresse suivante :
[Exercice-Artistes.sql](Exercice-Artistes.sql)

Une fois ce fichier téléchargé, vous l’importerez dans une nouvelle base de données.

<img src="Exercice-Artistes.png" style="display:block; margin:auto;" />

---

### Question 1

Lister les clubs auxquels sont inscrits les 'Bob' _(sans doublons)_

```sql
SELECT club.nom
FROM club
  JOIN user_club ON club.id = user_club.club_id
  JOIN user ON user_club.user_id = user.id
WHERE user.prenom = 'Bob'
GROUP BY club.id;
```

### Question 2

Lister les membres du club de foot.

```sql
SELECT user.*
FROM club
  JOIN user_club ON club.id = user_club.club_id
  JOIN user ON user_club.user_id = user.id
WHERE club.nom = 'foot';
```

### Question 3

Nommer le `club` le plus populaire. _(càd qui a le plus de membres)_

```sql
-- pour prendre en compte les clubs qui seraient ex æquo

SELECT
  GROUP_CONCAT(temp.nom ORDER BY temp.nom ASC SEPARATOR ', '),
  temp.compteur
FROM (
  SELECT
    club.nom,
    COUNT(club.id) AS compteur
  FROM club
    INNER JOIN user_club ON club.id = user_club.club_id
    INNER JOIN user ON user_club.user_id = user.id
  GROUP BY club.nom
  ORDER BY compteur DESC
) AS temp
GROUP BY temp.compteur
ORDER BY temp.compteur DESC
LIMIT 1
```

### Question 4

Trouver les `user` qui ne sont inscrit à aucun `club`.

1. En utilisant `EXISTS`

   ```sql
   SELECT `user`.nom, `user`.prenom
   FROM `user`
   WHERE NOT EXISTS (
     SELECT * from `user_club`
     WHERE user.id = user_club.user_id
   );
   ```

2. En utilisant `JOIN`

   ```sql
   SELECT user.nom, user.prenom
   FROM user
     LEFT JOIN user_club ON user_club.user_id = user.id
   WHERE user_club.id IS NULL;
   ```

### Question 5

Trouver les clubs auxquels n'est pas inscrit Bob Marley.

1. En utilisant `EXISTS`

   ```sql
   SELECT club.nom
   FROM club
   WHERE
     NOT EXISTS (
       SELECT `user`.id
       FROM `user`
         JOIN user_club ON `user`.id = user_club.user_id
         WHERE
           club.id = user_club.club_id
           AND `user`.prenom = 'Bob'
           AND `user`.nom = 'Marley'
     );
   ```

2. En utilisant `IN`

   ```sql
   -- solution la plus simple
   SELECT u.prenom, u.nom, c.nom
   FROM
     `user` AS u,
     club AS c
   WHERE
     c.id NOT IN (
       SELECT uc.club_id
       FROM user_club AS uc
       WHERE uc.user_id = u.id
     )
     AND u.nom = 'Marley'
     AND u.prenom = 'Bob';
   ```

3. En utilisant `JOIN`

   ```sql
   -- cette solution pourra utiliser pleinement les index
   SELECT club.nom
   FROM
     club
     LEFT JOIN user_club ON user_club.club_id = club.id
     LEFT JOIN `user` ON (
       `user`.id = user_club.user_id
       AND `user`.nom = 'Marley'
       AND `user`.prenom = 'Bob'
     )
   WHERE user_club.id IS NULL;
   ```

   ```sql
   -- plus élégante :)
   SELECT c.nom
   FROM
     `user` AS u
     CROSS JOIN club AS c
     LEFT JOIN user_club AS uc ON (
       uc.user_id = u.id
       AND c.id = uc.club_id
     )
   WHERE
     uc.id IS NULL
     AND u.prenom = 'Bob'
     AND u.nom = 'Marley';

   +---------+
   | nom     |
   +---------+
   | Golf    |
   | Tennis  |
   | Échecs  |
   +---------+
   ```

### Question 6

Inscrire Bob Marley à tous les clubs en une seule requête.

```sql
INSERT INTO user_club (user_id, club_id)
SELECT `user`.id, club.id
FROM
  club
  LEFT JOIN user_club AS uc ON uc.club_id = club.id
  LEFT JOIN `user` ON (
    `user`.id = uc.user_id
    AND `user`.nom = 'Marley'
    AND `user`.prenom = 'Bob'
  )
WHERE uc.id IS NULL;
```

### Question 7

Virer tous les 'Bob' du club de foot en une seule requête.

### Question 8

Recherche veufs et orphelins :

- Trouver les clubs qui n'ont aucun membre.
- Trouver les membres qui ne sont inscrit à aucun club.

### Question 9

Lister tous les utilisateurs ainsi que les clubs auxquels ils sont inscrit.

Une ligne par membre, le nom des clubs séparé par une virgule (`', '`). S'il n'est inscrit à aucun club que soit mentionné "Membre d'aucun club." à la place.

### Question 10

Ajouter les contraintes (`CONSTRAINT`) de sorte que :

- on ne puisse supprimer un club qui possède des membres,
- si on supprime un compte `user` il soit désinscrit des clubs. _(Càd que les entrées correspondantes soient supprimées de la table `user_club`.)_
