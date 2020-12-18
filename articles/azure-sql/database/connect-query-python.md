---
title: Använd python för att fråga en databas
description: Det här avsnittet visar hur du använder python för att skapa ett program som ansluter till en databas i Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5bad2293078711456ff06d998c43b87ecc1c9b76
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674280"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Snabb start: Använd python för att fråga en databas i Azure SQL Database eller Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I den här snabb starten använder du python för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans och använda T-SQL-uttryck för att fråga data.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 och relaterad program vara

  # <a name="macos"></a>[macOS](#tab/macos)

  Om du vill installera homebrew och python, ODBC-drivrutinen och SQLCMD och python-drivrutinen för SQL Server, använder du steg **1,2**, **1,3** och **2,1** i [skapa python-appar med SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Mer information finns i [Microsoft ODBC-drivrutin på MacOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Använd för att installera python och andra nödvändiga paket `sudo apt-get install python python-pip gcc g++ build-essential` .

  Om du vill installera ODBC-drivrutinen, SQLCMD och python-drivrutinen för SQL Server, se [Konfigurera en miljö för Pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Mer information finns i [Microsoft ODBC-drivrutin på Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Om du vill installera python, ODBC-drivrutinen och SQLCMD och python-drivrutinen för SQL Server, se [Konfigurera en miljö för Pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Mer information finns i [Microsoft ODBC-drivrutin](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
För att ytterligare utforska python och databasen i Azure SQL Database, se [Azure SQL Database bibliotek för python](/python/api/overview/azure/sql), [pyodbc-lagringsplatsen](https://github.com/mkleehammer/pyodbc/wiki/)och ett [pyodbc-exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till databasen i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för databasen i Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server på Azure VM. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till en SQL Server instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Skapa en kod för att fråga databasen 

1. Skapa en ny fil med namnet *sqltest.py* i en textredigerare.  
   
1. Lägg till följande kod. Ersätt dina egna värden för \<server> , \<database> , \<username> och \<password> .
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommando i en kommandotolk:

   ```cmd
   python sqltest.py
   ```

1. Kontrol lera att de 20 översta kategorierna/produkt raderna returneras och stäng sedan kommando fönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft python-drivrutiner för SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)
