---
title: 'Snabb start: ansluta med python-Azure Database for PostgreSQL – flexibel Server'
description: Den här snabb starten innehåller flera python-kod exempel som du kan använda för att ansluta och fråga efter data från Azure Database for PostgreSQL-flexibel Server.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 948972c1d47e550395a280e62c0491800e716663
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550650"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Snabb start: Använd python för att ansluta och fråga efter data i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

I den här snabb starten ansluter du till en Azure Database for PostgreSQL-flexibel server med python. Sedan använder du SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac, Ubuntu Linux och Windows-plattformar. 

Den här artikeln förutsätter att du är van att utveckla med python, men du är inte nybörjare på att arbeta med Azure Database for PostgreSQL-flexibel Server.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En Azure Database for PostgreSQL-flexibel Server. För att skapa en flexibel Server, se [skapa en Azure Database for PostgreSQL-flexibel server med Azure Portal](./quickstart-create-server-portal.md).
* [Python](https://www.python.org/downloads/) 2,7 eller 3.6 +.
* Senaste installations program för [pip](https://pip.pypa.io/en/stable/installing/) -paketet.

## <a name="preparing-your-client-workstation"></a>Förbereda klient arbets stationen
- Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server. Läs om [hur du skapar och hanterar Azure Database for PostgreSQL-flexibla Server virtuella nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Läs om hur du [skapar och hanterar Azure Database for PostgreSQL-flexibla Server brand Väggs regler med hjälp av Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-the-python-libraries-for-postgresql"></a>Installera python-biblioteken för PostgreSQL
Med modulen [psycopg2](https://pypi.python.org/pypi/psycopg2/) kan du ansluta till och fråga en PostgreSQL-databas och den är tillgänglig som Linux-, MacOS-eller Windows [Wheel](https://pythonwheels.com/) -paket. Installera den binära versionen av modulen, inklusive alla beroenden. Mer information om `psycopg2` installation och krav finns i [installation](http://initd.org/psycopg/docs/install.html). 

Installera `psycopg2` genom att öppna en terminal eller kommando tolk och köra kommandot `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Hämta information om databas anslutning
Att ansluta till en Azure Database for PostgreSQL-flexibel Server kräver det fullständigt kvalificerade Server namnet och inloggnings uppgifterna. Du kan hämta den här informationen från Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)söker du efter och väljer ditt flexibla Server namn. 
2. På sidan **Översikt** för servern kopierar du det fullständigt kvalificerade **Server namnet** och **administratörens användar namn**. Det fullständigt kvalificerade **Server namnet** är alltid av formatet *\<my-server-name> . postgres.Database.Azure.com*.

   Du behöver också ditt administratörs lösen ord. Om du glömmer bort det kan du återställa det från översikts sidan. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Så här kör du python-exemplen

För varje kod exempel i den här artikeln:

1. Skapa en ny fil i en text redigerare. 

1. Lägg till kod exemplet i filen. I koden ersätter du:
   - `<server-name>` och `<admin-username>` med de värden som du kopierade från Azure Portal.
   - `<admin-password>` med lösen ordet för servern.
   - `<database-name>` med namnet på din Azure Database for PostgreSQL-flexibla Server databasen. En standard databas med namnet *postgres* skapades automatiskt när du skapade servern. Du kan byta namn på databasen eller skapa en ny databas med hjälp av SQL-kommandon. 

1. Spara filen i projektmappen med fil namns tillägget *. py* , till exempel *postgres-INSERT.py*. För Windows kontrollerar du att UTF-8-kodning är markerat när du sparar filen. 

1. Om du vill köra filen ändrar du till projektmappen i ett kommando rads gränssnitt och skriver `python` följt av fil namnet, till exempel `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Skapa en tabell och infoga data
Följande kod exempel ansluter till din Azure Database for PostgreSQL-flexibla Server databasen med hjälp av funktionen [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) och läser in data med en SQL **insert** -instruktion. Funktionen [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) kör SQL-frågan mot databasen. 

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

![Kommandoradsutdata](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Läsa data
Följande kod exempel ansluter till din Azure Database for PostgreSQL-flexibla Server databasen och använder [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Select** -instruktionen för att läsa data. Den här funktionen accepterar en fråga och returnerar en resultat uppsättning att iterera över med hjälp av [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Uppdatera data
Följande kod exempel ansluter till din Azure Database for PostgreSQL-flexibla Server databasen och använder [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Update** -instruktionen för att uppdatera data. 

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
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Ta bort data
Följande kod exempel ansluter till din Azure Database for PostgreSQL-flexibla Server databasen och använder [cursor.exesöta](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **Delete** -instruktionen för att ta bort ett lager objekt som du har infogat tidigare. 

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
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med dumpa och Återställ](../howto-migrate-using-dump-and-restore.md)
