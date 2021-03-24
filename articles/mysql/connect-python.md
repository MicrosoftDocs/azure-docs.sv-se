---
title: 'Snabb start: Anslut med python-Azure Database for MySQL'
description: I den här snabbstarten finns flera kodexempel i Python som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889033"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: Använd python för att ansluta och fråga efter data i Azure Database for MySQL

I den här snabb starten ansluter du till en Azure Database for MySQL med hjälp av python. Sedan använder du SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac, Ubuntu Linux och Windows-plattformar. 

## <a name="prerequisites"></a>Förutsättningar
För den här snabb starten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free).
- Skapa en Azure Database for MySQL enskild server med [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> eller [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) om du inte har något.
- Baserat på om du använder offentlig eller privat åtkomst utför du **en** av åtgärderna nedan för att aktivera anslutningen.

   |Action| Anslutningsmetod|Instruktionsguide|
   |:--------- |:--------- |:--------- |
   | **Konfigurera brandväggsregler** | Offentliga | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Konfigurera tjänstens slut punkt** | Offentliga | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Konfigurera privat länk** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Skapa en databas och icke-administratörs användare](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Installera Python och MySQL Connector

Installera python och MySQL Connector för python på datorn med hjälp av följande steg: 

> [!NOTE]
> Den här snabb starten använder [MySQL Connector/python Developer Guide](https://dev.mysql.com/doc/connector-python/en/).

1. Hämta och installera [Python 3,7 eller senare](https://www.python.org/downloads/) för ditt operativ system. Se till att lägga till python till din `PATH` , eftersom MySQL-anslutaren kräver det.
   
2. Öppna en kommando tolk eller ett `bash` gränssnitt och kontrol lera python-versionen genom `python -V` att köra med versalen V-växeln.
   
3. `pip`Paket installations programmet ingår i de senaste versionerna av python. Uppdatera `pip` till den senaste versionen genom att köra `pip install -U pip` . 
   
   Om `pip` inte är installerat kan du ladda ned och installera det med `get-pip.py` . Mer information finns i [installationen](https://pip.pypa.io/en/stable/installing/). 
   
4. Använd `pip` för att installera MySQL Connector för python och dess beroenden:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta anslutnings informationen som du behöver för att ansluta till Azure Database for MySQL från Azure Portal. Du behöver Server namnet, databas namnet och inloggnings uppgifterna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
   
1. I Portal Sök fältet söker du efter och väljer den Azure Database for MySQL server som du skapade, till exempel **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::
   
1. På sidan **Översikt** av servern anger du **Server namnet** och **inloggnings namnet för Server administratören**. Om du glömmer ditt lösen ord kan du också återställa lösen ordet från den här sidan.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL server namn 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Steg 1: skapa en tabell och infoga data

Använd följande kod för att ansluta till servern och databasen, skapa en tabell och läsa in data med hjälp av en **insert** SQL-instruktion. Koden importerar MySQL. Connector-biblioteket och använder metoden:
- [Connect ()-](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) funktionen för att ansluta till Azure Database for MySQL med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i konfigurations samlingen. 
- Metoden [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 
- [cursor. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) när du är färdig med en markör.
- [ansluten. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) för att stänga anslutningen till anslutningen.

> [!IMPORTANT]
> - SSL är aktiverat som standard. Du kan behöva hämta [DIGICERTGLOBALROOTG2 SSL-certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) för att kunna ansluta från den lokala miljön.
> - Ersätt `<mydemoserver>` `<myadmin>` `<mypassword>` `<mydatabase>` plats hållarna,, och med värdena för MySQL-servern och databasen.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Har du problem? Berätta för oss](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Steg 2: Läs data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. Koden importerar MySQL. Connector-biblioteket och använder [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) -metoden kör SQL-frågan mot MySQL-databasen. 

Koden läser data rader med hjälp av metoden [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , behåller resultat uppsättningen i en samlings rad och använder en `for` iterator för att loopa över raderna.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Steg 3: uppdatera data

Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion. Koden importerar MySQL. Connector-biblioteket och använder [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) -metoden kör SQL-frågan mot MySQL-databasen. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Steg 4: ta bort data

Använd följande kod för att ansluta och ta bort data med en **DELETE**-SQL-instruktion. Koden importerar MySQL. Connector-biblioteket och använder [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) -metoden kör SQL-frågan mot MySQL-databasen. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [Hantera Azure Database for MySQL server med CLI](./how-to-manage-single-server-cli.md)

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/mysql-doc-feedback)
