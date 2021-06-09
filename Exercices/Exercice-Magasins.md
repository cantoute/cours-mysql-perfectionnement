# MAGASINS

## Soit le schéma de la relation magasins suivant :

### Magasin

| id  | nom       | categorie    | ville            | gerant           |
| --- | --------- | ------------ | ---------------- | ---------------- |
| 1   | TASCORAMA | Bricolage    | Bron             | Yvan Declou      |
| 2   | TASCORAMA | Bricolage    | Rillieux-la-Pape | Eddy Visse       |
| 3   | LECIO     | Textile      | Lyon             | Ella Laclasse    |
| 4   | SAPEY     | Textile      | Lyon             | Côme Jamet       |
| 5   | LOUBANGER | Informatique | Lyon             | Yann Apadebeugue |
| 6   | VBH       | Bricolage    | Paris            | Elvis Tourne     |

## Exprimer en langage naturel ce que retournent les requêtes suivantes :

1.  ```sql
    SELECT nom FROM magasin;
    ```

2.  ```sql
    SELECT DISTINCT categorie
    FROM magasin WHERE ville = 'Lyon';
    ```

3.  ```sql
    SELECT ∗
    FROM magasin
    WHERE ville != 'Lyon' AND categorie = 'Bricolage'
    ORDER BY nom;
    ```

4.  ```sql
    SELECT DISTINCT m1.id, m2.id
    FROM magasin m1
      JOIN magasin m2
        ON (
          m1.ville = m2.ville
          AND m1.id != m2.id
        );
    ```

## Quelle modification apporteriez-vous à la dernière requête pour éviter d’avoir les pairs symétriques dans le résultat (par exemple : (3,4) et (4,3)) ?
