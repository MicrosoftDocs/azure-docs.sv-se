---
title: 'Snabbstart: Ansluta med C++ – Azure Database for MySQL'
description: Den här snabbstarten innehåller ett kodexempel i C++ som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 723d3d3bfb36b17ebdacf9d5961b10071a12ab42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863469"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabbstart: Använda Connector/C++ för att ansluta och fråga efter data i Azure Database for MySQL

Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL med hjälp av ett C++-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla med C++ och att du inte har arbetat med Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar

I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Du måste också:
- Installera [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)
- Installera [Visual Studio](https://www.visualstudio.com/downloads/)
- Installera [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Installera [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Kontrollera att IP-adressen som du ansluter från har lagts till serverns brandväggsregler med hjälp [av Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-visual-studio-and-net"></a>Installera Visual Studio och .NET
Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med hjälp av .NET.

### <a name="windows"></a>**Windows**
- Installera Visual Studio 2019 Community. Visual Studio 2019 Community är en komplett, utökningsbar och kostnadsfri IDE. Med den här IDE:n kan du skapa moderna program för Android, iOS, Windows, webb- och databasprogram och molntjänster. Du kan installera det fullständiga .NET Framework eller bara .NET Core. Kodfragmenten i snabbstarten fungerar med båda. Om du redan har Visual Studio installerat på datorn kan du hoppa över de kommande två stegen.
   1. Ladda ned [Visual Studio 2019-installationsprogrammet.](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) 
   2. Kör installationsprogrammet och följ anvisningarna för att slutföra installationen.

### <a name="configure-visual-studio"></a>**Konfigurera Visual Studio**
1. Från Visual Studio, Project -> Properties -> Linker -> General > Additional Library Directories, lägger du till katalogen "\lib\opt" (till exempel: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt) för C++-anslutningsprogrammet.
2. Från Visual Studio: Projekt -> Egenskaper -> C/C++ -> Allmänt > Ytterligare inkluderingskataloger:
   - Lägg till katalogen "\include" för c++ connector (till exempel: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\include \) .
   - Lägg till Boost-bibliotekets rotkatalog (till exempel: C:\boost_1_64_0 \) .
3. Från Visual Studio: Projekt -> Egenskaper -> Linker -> Indata -> Ytterligare beroenden, lägg till **mysqlcppconn.lib** i textfältet.
4. Antingen kopierar du **mysqlcppconn.dll** från biblioteksmappen C++ connector i steg 3 till samma katalog som den körbara programfilen eller lägger till den i miljövariabeln så att programmet kan hitta den.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med hjälp av **sql-CREATE TABLE** **och INSERT INTO.** Koden använder klassen sql::Driver med metoden connect() för att upprätta en anslutning till MySQL. Sedan används metoden createStatement() och execute() för att köra databaskommandona. 

Ersätt parametrarna Host, DBName, User och Password. Du kan ersätta parametrarna med de värden som du angav när du skapade servern och databasen. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. Koden använder klassen sql::Driver med metoden connect() för att upprätta en anslutning till MySQL. Sedan används metoden prepareStatement() och executeQuery() för att köra de valda kommandona. Härnäst använder koden next () för att gå vidare till posterna i resultaten. Slutligen använder koden getInt() och getString() för att parsa värdena i posten.

Ersätt parametrarna Host, DBName, User och Password. Du kan ersätta parametrarna med de värden som du angav när du skapade servern och databasen. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och läsa data med SQL-instruktionen **UPDATE**. Koden använder klassen sql::Driver med metoden connect() för att upprätta en anslutning till MySQL. Sedan används metoden prepareStatement() och executeQuery() för att köra uppdateringskommandona. 

Ersätt parametrarna Host, DBName, User och Password. Du kan ersätta parametrarna med de värden som du angav när du skapade servern och databasen. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion. Koden använder klassen sql::Driver med metoden connect() för att upprätta en anslutning till MySQL. Sedan används metoden prepareStatement() och executeQuery() för att köra borttagningskommandona.

Ersätt parametrarna Host, DBName, User och Password. Du kan ersätta parametrarna med de värden som du angav när du skapade servern och databasen. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används i den här snabbstarten tar du bort resursgruppen med följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning](concepts-migrate-dump-restore.md)
