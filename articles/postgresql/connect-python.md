---
title: 'Snabb start: ansluta med python – Azure Database for PostgreSQL-enskild server'
description: Den här snabb starten innehåller python-kod exempel som du kan använda för att ansluta och fråga efter data från Azure Database for PostgreSQL-enskild server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998948"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabb start: Använd python för att ansluta och fråga efter data i Azure Database for PostgreSQL-enskild server

I den här snabb starten får du lära dig hur du ansluter till databasen på Azure Database for PostgreSQL enskild server och kör SQL-uttryck för att fråga med python på macOS, Ubuntu Linux eller Windows.

> [!TIP]
> Om du vill bygga ett django-program med PostgreSQL kan du checka in självstudien, [distribuera en django-webbapp med en postgresql-](../app-service/tutorial-python-postgresql-app.md) självstudie.


## <a name="prerequisites"></a>Förutsättningar
För den här snabb starten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free).
- Skapa en Azure Database for PostgreSQL enskild server med [Azure Portal](./quickstart-create-server-database-portal.md) <br/> eller [Azure CLI](./quickstart-create-server-database-azure-cli.md) om du inte har något.
- Baserat på om du använder offentlig eller privat åtkomst utför du **en** av åtgärderna nedan för att aktivera anslutningen.

  |Action| Anslutningsmetod|Instruktionsguide|
  |:--------- |:--------- |:--------- |
  | **Konfigurera brandväggsregler** | Offentliga | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Konfigurera tjänstens slut punkt** | Offentliga | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Konfigurera privat länk** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9 + eller 3.4 +.

- Senaste installations program för [pip](https://pip.pypa.io/en/stable/installing/) -paketet.
- Installera [psycopg2](https://pypi.python.org/pypi/psycopg2/) med `pip install psycopg2` i en terminal-eller kommando tolks fönster. Mer information finns i [så här installerar `psycopg2` du ](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Hämta information om databas anslutning
Att ansluta till en Azure Database for PostgreSQL-databas kräver det fullständigt kvalificerade Server namnet och inloggnings uppgifterna. Du kan hämta den här informationen från Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)söker du efter och väljer Azure Database for postgresql server namnet.
1. På sidan **Översikt** för servern kopierar du det fullständigt kvalificerade **Server namnet** och **administratörens användar namn**. Det fullständigt kvalificerade **Server namnet** är alltid av formatet *\<my-server-name> . postgres.Database.Azure.com* och **administratörens användar namn** är alltid i formatet *\<my-admin-username>@\<my-server-name>* .

   Du behöver också ditt administratörs lösen ord. Om du glömmer bort det kan du återställa det från den här sidan.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL-servernamn":::

> [!IMPORTANT]
>  Ersätt följande värden:
>   - `<server-name>` och `<admin-username>` med de värden som du kopierade från Azure Portal.
>   - `<admin-password>` med lösen ordet för servern.
>   - `<database-name>` en standard databas med namnet *postgres* skapades automatiskt när du skapade servern. Du kan byta namn på databasen eller [skapa en ny databas](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) med hjälp av SQL-kommandon.

## <a name="step-1-connect-and-insert-data"></a>Steg 1: Anslut och infoga data
Följande kod exempel ansluter till Azure Database for PostgreSQL-databasen med hjälp av
-  [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) -funktionen och läser in data med en SQL **insert** -instruktion.
- [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) -funktionen kör SQL-frågan mot databasen.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

När koden körs skapas följande utdata:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Kommandoradsutdata":::


[Har du problem? Berätta för oss](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Steg 2: Läs data
Följande kod exempel ansluter till din Azure Database for PostgreSQL-databas och använder
- [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Select** -instruktionen för att läsa data.
- [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) accepterar en fråga och returnerar en resultat uppsättning att iterera över med hjälp av

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Har du problem? Berätta för oss](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Steg 3: uppdatera data
I följande kod exempel används [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Update** -instruktionen för att uppdatera data.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Har du problem? Berätta för oss](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Steg 5: ta bort data
Följande kod exempel kör [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Delete** -instruktionen för att ta bort ett lager objekt som du har infogat tidigare.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Har du problem? Berätta för oss](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Hantera Azure Database for MySQL server med hjälp av portalen](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Hantera Azure Database for MySQL server med CLI](./how-to-manage-server-cli.md)<br/>

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/postgres-doc-feedback)
