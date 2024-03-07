**Exercice 1 : Création utilisateurs et attribution des privilèges**

1. Créez l'utilisateur vente_user qui aura uniquement le droit d'insérer et de lire dans la table Ventes.

```sql
CREATE USER vente_user IDENTIFIED BY 'mot_de_passe';
GRANT INSERT ON Ventes TO vente_user;
GRANT SELECT ON Ventes TO vente_user;
```

2. Créez l'utilisateur stock_user qui aura le droit de mettre à jour et de lire dans la table Produits.

```sql
CREATE USER stock_user IDENTIFIED BY 'mot_de_passe';
GRANT UPDATE ON Produits TO stock_user;
GRANT SELECT ON Produits TO stock_user;
```

```sql
SHOW GRANTS FOR vente_user;
SHOW GRANTS FOR stock_user;
```

**Exercice 2 : Restriction d'accès par adresse IP**

1. Créer l'utilisateur `remote_user` restreint à l'adresse IP `192.168.1.100` avec accès en lecture à la table `Clients`.

```sql
CREATE USER remote_user IDENTIFIED BY 'mot_de_passe';
GRANT SELECT ON Clients TO remote_user;
GRANT USAGE ON *.* TO remote_user@'192.168.1.100';
```

**Exercice 3 : Gestion des rôles**

1. Créer le rôle `gestion_client` avec les privilèges de lecture et de mise à jour sur la table `Clients`.

```sql
CREATE ROLE gestion_client;
GRANT SELECT, UPDATE ON Clients TO gestion_client;
```

2. Créer l'utilisateur `client_manager` et lui attribuer le rôle `gestion_client`.

```sql
CREATE USER client_manager IDENTIFIED BY 'mot_de_passe';
GRANT gestion_client TO client_manager;
```

**Exercice 4 : Révocation de privilèges**

1. Donner à `vente_user` le privilège de suppression sur la table `Ventes`.

```sql
GRANT DELETE ON Ventes TO vente_user;
```

2. Révoquer le privilège de suppression de `vente_user` sur la table `Ventes`.

```sql
REVOKE DELETE ON Ventes FROM vente_user;
```

**Exercice 5 : Suppression**

1. Supprimer l'utilisateur `remote_user`.

```sql
DROP USER remote_user;
```

2. Supprimer le rôle `gestion_client`.

```sql
DROP ROLE gestion_client;
```