# Exercices les Artistes

Le travail est à réaliser à partir du fichier disponible à l’adresse suivante :
[Exercice-Artistes.sql](Exercice-Artistes.sql)

Une fois ce fichier téléchargé, vous l’importerez dans une nouvelle base de données.

<img src="Exercice-Artistes.png" style="display:block; margin:auto;" />

---

### Question 1

Lister les clubs auxquels sont inscrits les 'Bob' _(sans doublons)_

### Question 2

Lister les membres du club de foot.

### Question 3

Nommer le `club` le plus populaire. _(càd qui a le plus de membres)_

### Question 4

Trouver les `user` qui ne sont inscrit à aucun `club`.

1. En utilisant `EXISTS`

2. En utilisant `LEFT JOIN`

   _Astuce : les conditions sur le user n'iront pas dans le `WHERE` mais dans les conditions du `JOIN`_

3. En utilisant `CROSS JOIN`

### Question 5

Trouver les clubs auxquels n'est pas inscrit Bob Marley.

1. En utilisant `EXISTS`

2. En utilisant `IN()`

3. En utilisant `LEFT JOIN`

4. En utilisant `CROSS JOIN`

### Question 6

Inscrire Bob Marley à tous les clubs en une seule requête.

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

Ajouter les contraintes (`CONSTRAINT` / `UNIQUE`) de sorte que :

- on ne puisse avoir deux membres avec même nom, prénom, date naissance et que l'index soit utilisable pour une recherche par nom.
- on ne puisse supprimer un club qui possède des membres,
- si on supprime un compte `user` il soit désinscrit des clubs. _(Càd que les entrées correspondantes soient supprimées de la table `user_club`.)_
