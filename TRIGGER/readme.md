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