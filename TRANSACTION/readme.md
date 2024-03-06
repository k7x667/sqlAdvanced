Exercice 1 : Contrôle de base des transactions

```sql
START TRANSACTION;

INSERT INTO Clients (nom, email, age) VALUES ('Jean Dupont', 'jean.dupont@example.com', 35);
INSERT INTO Clients (nom, email, age) VALUES ('Marie Martin', 'marie.martin@example.com', 28);

SELECT * FROM Clients;

UPDATE Produits SET stock = stock - 5 WHERE id_produit = 1;

SELECT * FROM Produits WHERE id_produit = 1;

ROLLBACK;

SELECT * FROM Clients;
SELECT * FROM Produits WHERE id_produit = 1;

```

Exercice 2 : Validation de transactions

```sql
START TRANSACTION;

INSERT INTO Clients (nom, email, age) VALUES ('Sophie Dupont', 'sophie.dupont@example.com', 30);
INSERT INTO Ventes (id_client, id_produit, quantite) VALUES (1, 2, 3);

COMMIT;

SELECT * FROM Clients;
SELECT * FROM Ventes;

```

Exercice 3 : Gestion des erreurs dans les transactions

```sql
START TRANSACTION;

INSERT INTO Produits (nom_produit, prix, stock) VALUES ('Produit Test', 50.00, 10);

INSERT INTO Produits (nom_produit, prix) VALUES ('Produit Test 2', 75.00);

ROLLBACK;

SELECT * FROM Produits;

```

Exercice 4 : Utilisez les points de sauvegarde

```sql
START TRANSACTION;

SAVEPOINT SP1;
INSERT INTO Ventes (id_client, id_produit, quantite) VALUES (1, 1, 2);
UPDATE Produits SET stock = stock - 2 WHERE id_produit = 1;

SAVEPOINT SP2;
INSERT INTO Ventes (id_client, id_produit, quantite) VALUES (2, 3, 1);
UPDATE Produits SET stock = stock - 1 WHERE id_produit = 3;

ROLLBACK TO SP2;

COMMIT;

SELECT * FROM Ventes;
SELECT * FROM Produits WHERE id_produit = 1 OR id_produit = 3;

```

Exercice 5 : Gestion du stock avec verrouillage de table

````sql
START TRANSACTION;
LOCK TABLE Produits WRITE;
SELECT stock FROM Produits WHERE id_produit = 1;

UPDATE Produits SET stock = stock - 3 WHERE id_produit = 1;

COMMIT;
UNLOCK TABLES;

START TRANSACTION;
UPDATE Produits SET stock = stock - 1 WHERE id_produit = 1;
ROLLBACK;
```