Vue combinant les utilisateurs et leurs commandes :

```sql
CREATE VIEW viewUserCommand AS
SELECT u.nom, c.id AS id_commande, c.date, c.montant
FROM utilisateurs u
JOIN commandes c ON u.id = c.id_utilisateur;
```

Récupérer les personnes qui ont dépensé + de 50 euros :

```sql
SELECT nom
FROM viewUserCommand
GROUP BY nom
HAVING SUM(montant) > 50;

```

Calculer le montant total dépensé :

```sql
SELECT SUM(montant) AS montant_total
FROM viewUserCommand;

```

Calculer le cumul par jour des sommes reçues, de la date la plus récente à la plus ancienne :

```sql
SELECT date, SUM(montant) AS montant_total
FROM viewUserCommand
GROUP BY date
ORDER BY date DESC;

```

Classer les meilleurs clients :

```sql
SELECT nom, SUM(montant) AS total_depense
FROM viewUserCommand
GROUP BY nom
ORDER BY total_depense DESC;

```

Pour modifier la vue afin d'y intégrer l'ID de commande et l'ID de l'utilisateur :

```sql
CREATE OR REPLACE VIEW viewUserCommand AS
SELECT u.id AS id_utilisateur, u.nom, c.id AS id_commande, c.date, c.montant
FROM utilisateurs u
JOIN commandes c ON u.id = c.id_utilisateur;

```

Pour mettre à jour les valeurs de l'utilisateur avec l’ID 1 :

```sql
UPDATE commandes
SET montant = montant + 15
WHERE id_utilisateur = 1;

UPDATE commandes
SET montant = montant - 5
WHERE id_utilisateur = 1
AND montant > 200;
```