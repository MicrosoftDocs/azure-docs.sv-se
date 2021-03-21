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
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491970"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Snabb start: Använd python för att fråga en databas

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

I den här snabb starten använder du python för att ansluta till Azure SQL Database, Azure SQL-hanterad instans eller Synapse SQL Database och använda T-SQL-uttryck för att fråga efter data.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- En databas där du kommer att köra en fråga.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 och relaterad program vara
    

    |**Åtgärd**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Installera ODBC-drivrutinen, SQLCMD och python-drivrutinen för SQL Server|Använd steg **1,2**, **1,3** och **2,1** i [skapa python-appar med SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Då installeras även installera homebrew och python.       |[Konfigurera en miljö för pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Konfigurera en miljö för Pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Installera python och andra nödvändiga paket|    |Använd `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Ytterligare information|[Microsoft ODBC-drivrutin på macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Microsoft ODBC-drivrutin på Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Microsoft ODBC-drivrutin på Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



För att ytterligare utforska python och databasen i Azure SQL Database, se [Azure SQL Database bibliotek för python](/python/api/overview/azure/sql), [pyodbc-lagringsplatsen](https://github.com/mkleehammer/pyodbc/wiki/)och ett [pyodbc-exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Skapa en kod för att fråga databasen 

1. Skapa en ny fil med namnet *sqltest.py* i en textredigerare.  
   
1. Lägg till följande kod. Hämta anslutnings informationen från avsnittet krav och ersätt dina egna värden för \<server> , \<database> , \<username> och \<password> .
   
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

1. Kontrol lera att databaserna och deras sorteringar returneras och stäng sedan kommando fönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft python-drivrutiner för SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)
