---
title: 'Snabb start: ansluta med python-Azure Database for MySQL – flexibel Server'
description: Den här snabb starten innehåller flera python-kod exempel som du kan använda för att ansluta och fråga efter data från Azure Database for MySQL-flexibel Server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: ff14bb1daeef6fc54ee5d11632ad98a29db2a172
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90947890"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Snabb start: Använd python för att ansluta och fråga efter data i Azure Database for MySQL-flexibel Server

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

I den här snabb starten ansluter du till en Azure Database for MySQL flexibel server med python. Sedan använder du SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac, Ubuntu Linux och Windows-plattformar. 

Den här artikeln förutsätter att du är van att utveckla med python, men du är inte nybörjare på att arbeta med Azure Database for MySQL flexibel Server.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En Azure Database for MySQL flexibel Server. Om du vill skapa en flexibel Server kan du läsa [skapa en Azure Database for MySQL flexibel server med Azure Portal](./quickstart-create-server-portal.md) eller [skapa en Azure Database for MySQL flexibel server med hjälp av Azure CLI](./quickstart-create-server-cli.md).

## <a name="preparing-your-client-workstation"></a>Förbereda klient arbets stationen
- Om du har skapat din flexibla server med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i VNet som skapats med din flexibla Server. Se [skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Om du har skapat din flexibla server med *offentlig åtkomst (tillåtna IP-adresser)* kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Se [skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Installera Python och MySQL Connector

Installera python och MySQL Connector för python på datorn med hjälp av följande steg: 

> [!NOTE]
> Den här snabb starten använder en RAW SQL-frågeprincip för att ansluta till MySQL. Om du använder ett webb ramverk använder du den rekommenderade anslutningen för ramverket, till exempel [MySqlClient](https://pypi.org/project/mysqlclient/) för django.

1. Hämta och installera [Python 3,7 eller senare](https://www.python.org/downloads/) för ditt operativ system. Se till att lägga till python till din `PATH` , eftersom MySQL-anslutaren kräver det.
   
1. Öppna en kommando tolk eller ett `bash` gränssnitt och kontrol lera python-versionen genom `python -V` att köra med versalen V-växeln.
   
1. `pip`Paket installations programmet ingår i de senaste versionerna av python. Uppdatera `pip` till den senaste versionen genom att köra `pip install -U pip` . 
   
   Om `pip` inte är installerat kan du ladda ned och installera det med `get-pip.py` . Mer information finns i [installationen](https://pip.pypa.io/en/stable/installing/). 
   
1. Använd `pip` för att installera MySQL Connector för python och dess beroenden:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Du kan också installera python-anslutaren för MySQL från [MySQL.com](https://dev.mysql.com/downloads/connector/python/). Mer information om MySQL Connector för python finns i [hand boken för MySQL Connector/python Developer](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta anslutnings informationen du behöver för att ansluta till Azure Database for MySQL flexibel Server från Azure Portal. Du behöver Server namnet, databas namnet och inloggnings uppgifterna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
   
1. I Portal Sök fältet söker du efter och väljer den Azure Database for MySQL flexibla server som du skapade, till exempel **mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. På sidan **Översikt** av servern anger du **Server namnet** och **inloggnings namnet för Server administratören**. Om du glömmer ditt lösen ord kan du också återställa lösen ordet från den här sidan.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Kodexempel

### <a name="run-below-mentioned-python-code-samples"></a>Kör nedan angivna python-kod exempel
För varje kod exempel i den här artikeln:

1. Skapa en ny fil i en text redigerare.
1. Lägg till kod exemplet i filen. I koden ersätter du `<mydemoserver>` `<myadmin>` `<mypassword>` plats hållarna,, och `<mydatabase>` med värdena för MySQL-servern och databasen.
1. Spara filen i en projektmapp med fil namns tillägget *. py* , till exempel *C:\pythonmysql\createtable.py* eller */Home/username/pythonmysql/createTable.py*.
1. Du kör koden genom att öppna en kommando tolk eller ett `bash` gränssnitt och ändra katalogen till projektmappen, till exempel `cd pythonmysql` . Skriv `python` kommandot följt av fil namnet, till exempel `python createtable.py` , och tryck på RETUR. 
   
   > [!NOTE]
   > Om *python.exe* inte hittas i Windows kan du behöva lägga till python-sökvägen i miljövariabeln PATH, eller ange den fullständiga sökvägen till *python.exe*, till exempel `C:\python27\python.exe createtable.py` .

### <a name="create-a-table-and-insert-data"></a>Skapa en tabell och infoga data

Använd följande kod för att ansluta till servern och databasen, skapa en tabell och läsa in data med hjälp av en **insert** SQL-instruktion. 

Koden importerar MySQL. Connector-biblioteket och använder funktionen [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till en flexibel server med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i konfigurations samlingen. Koden använder en markör i anslutningen och metoden [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

### <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. 

Koden importerar MySQL. Connector-biblioteket och använder funktionen [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till en flexibel server med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i konfigurations samlingen. Koden använder en markör i anslutningen och metoden [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

Koden läser data rader med hjälp av metoden [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , behåller resultat uppsättningen i en samlings rad och använder en `for` iterator för att loopa över raderna.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="update-data"></a>Uppdatera data

Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion. 

Koden importerar MySQL. Connector-biblioteket och använder funktionen [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till en flexibel server med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i konfigurations samlingen. Koden använder en markör i anslutningen och metoden [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="delete-data"></a>Ta bort data

Använd följande kod för att ansluta och ta bort data med en **DELETE**-SQL-instruktion. 

Koden importerar MySQL. Connector-biblioteket och använder funktionen [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till en flexibel server med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i konfigurations samlingen. Koden använder en markör i anslutningen och metoden [cursor.exesöta ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Nästa steg
- [Krypterad anslutning med Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibel Server](./how-to-connect-tls-ssl.md).
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server brand Väggs regler med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).
