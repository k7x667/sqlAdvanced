Créer un déclencheur qui enregistre dans une table de journalisation chaque fois qu'un produit est modifié (par exemple, changement de prix ou de stock).

```sql
CREATE TABLE JournalProduits (
  id_produit INT NOT NULL,
  type_modification VARCHAR(255) NOT NULL,
  ancienne_valeur DECIMAL(10,2) NULL,
  nouvelle_valeur DECIMAL(10,2) NULL,
  date_modification TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

DELIMITER //

CREATE TRIGGER before_update_produits
BEFORE UPDATE ON Produits
FOR EACH ROW
BEGIN
  IF NEW.prix_unitaire != OLD.prix_unitaire OR NEW.stock != OLD.stock THEN
    INSERT INTO JournalProduits (id_produit, type_modification, ancienne_valeur, nouvelle_valeur)
    VALUES (OLD.id_produit,
            CASE
              WHEN NEW.prix_unitaire != OLD.prix_unitaire THEN 'Prix'
              WHEN NEW.stock != OLD.stock THEN 'Stock'
            END,
            CASE
              WHEN NEW.prix_unitaire != OLD.prix_unitaire THEN OLD.prix_unitaire
              WHEN NEW.stock != OLD.stock THEN OLD.stock
            END,
            CASE
              WHEN NEW.prix_unitaire != OLD.prix_unitaire THEN NEW.prix_unitaire
              WHEN NEW.stock != OLD.stock THEN NEW.stock
            END);
  END IF;
END;

//

DELIMITER ;
```

Assurer que les nouveaux produits insérés dans la table Produits ont un stock initial positif et un prix unitaire supérieur à zéro.

```sql
DELIMITER //

CREATE TRIGGER before_insert_produits
BEFORE INSERT ON Produits
FOR EACH ROW
BEGIN
  IF NEW.stock <= 0 OR NEW.prix_unitaire <= 0 THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Le stock et le prix unitaire doivent être positifs.';
  END IF;
END;

//

DELIMITER ;
```
Mettre à jour automatiquement un champ `disponible` (BOOLEAN) dans la table Produits en fonction du stock.

**1. Ajouter un champ `disponible` à la table Produits**

```sql
ALTER TABLE Produits
ADD disponible BOOLEAN NOT NULL DEFAULT TRUE;
```

**2. Créer deux déclencheurs : un BEFORE INSERT et un BEFORE UPDATE**

**Déclencheur BEFORE INSERT:**

```sql
DELIMITER //

CREATE TRIGGER before_insert_produits
BEFORE INSERT ON Produits
FOR EACH ROW
BEGIN
  SET NEW.disponible = IF(NEW.stock > 0, TRUE, FALSE);
END;

//

DELIMITER ;
```

**Déclencheur BEFORE UPDATE:**

```sql
DELIMITER //

CREATE TRIGGER before_update_produits
BEFORE UPDATE ON Produits
FOR EACH ROW
BEGIN
  SET NEW.disponible = IF(NEW.stock > 0, TRUE, FALSE);
END;

//

DELIMITER ;
```