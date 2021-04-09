---
title: Anslut och fråga Azure SQL Edge
description: Lär dig hur du ansluter till och frågar Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395214"
---
# <a name="connect-and-query-azure-sql-edge"></a>Anslut och fråga Azure SQL Edge

När du har distribuerat en behållare i Azure SQL Edge kan du ansluta till databas motorn från någon av följande platser:

- Inuti behållaren
- Från en annan Docker-behållare som körs på samma värd
- Från värddatorn
- Från andra klient datorer i nätverket

## <a name="tools-to-connect-to-azure-sql-edge"></a>Verktyg för att ansluta till Azure SQL Edge

Du kan ansluta till en instans av Azure SQL Edge-instansen från något av dessa vanliga verktyg:

* [SQLCMD](/sql/linux/sql-server-linux-setup-tools): SQLCMD-klient verktyg ingår redan i behållar avbildningen av Azure SQL Edge. Om du ansluter till en behållare som körs med ett interaktivt bash-gränssnitt kan du köra verktygen lokalt. SQL-klient verktyg är inte tillgängliga på ARM64-plattformen, eftersom de inte ingår i ARM64-versionen av SQL Edge-behållare. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Om du vill ansluta till en Azure SQL Edge-databasmotor från en nätverks dator behöver du följande:

- **IP-adress eller nätverks namn för värddatorn**: det här är värd datorn där Azure SQL Edge-behållaren körs.
- **Port mappning för Azure SQL Edge-container**: Detta är mappningen för Docker-containerns port till en port på värden. I behållaren mappas Azure SQL Edge alltid till port 1433. Du kan ändra detta om du vill. Om du vill ändra port numret uppdaterar du alternativet för att **skapa behållare** för Azure SQL Edge-modulen i Azure IoT Edge. I följande exempel mappas port 1433 i behållaren till port 1600 på värden.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Sa-lösenord för Azure SQL Edge-instansen**: Detta är det värde som anges för `SA_PASSWORD` miljövariabeln under distributionen av Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Ansluta till databas motorn inifrån behållaren

[Kommando rads verktygen för SQL Server](/sql/linux/sql-server-linux-setup-tools) ingår i behållar avbildningen av Azure SQL Edge. Om du ansluter till behållaren med en interaktiv kommando tolk kan du köra verktygen lokalt. SQL-klient verktyg är inte tillgängliga på ARM64-plattformen, eftersom de inte ingår i ARM64-versionen av SQL Edge-behållare. 

1. Använd `docker exec -it` kommandot för att starta ett interaktivt bash-gränssnitt i den behållare som körs. I följande exempel `e69e056c702d` är container-ID: t.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Du behöver inte alltid ange hela container-ID: t. Du behöver bara ange tillräckligt många tecken för att unikt identifiera det. I det här exemplet kan det vara tillräckligt för att använda `e6` eller `e69` , i stället för det fullständiga ID: t.

2. När du är i behållaren ansluter du lokalt med SQLCMD. SQLCMD finns inte som standard i sökvägen, så du måste ange den fullständiga sökvägen.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. När du är klar med SQLCMD skriver du `exit` .

4. När du är klar med den interaktiva kommando tolken skriver du `exit` . Din behållare fortsätter att köras när du har avslutat det interaktiva bash-gränssnittet.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Anslut till Azure SQL Edge från en annan behållare på samma värd

Eftersom två behållare som körs på samma värd finns i samma Docker-nätverk, kan du enkelt komma åt dem med hjälp av behållarens namn och port adressen för tjänsten. Om du till exempel ansluter till instansen av Azure SQL Edge från en annan python-modul (container) på samma värd kan du använda en anslutnings sträng som liknar följande. (Det här exemplet förutsätter att Azure SQL Edge är konfigurerat för att lyssna på standard porten.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Ansluta till Azure SQL Edge från en annan nätverks dator

Du kanske vill ansluta till instansen av Azure SQL Edge från en annan dator i nätverket. Det gör du genom att använda IP-adressen för Docker-värden och den värd port som Azure SQL Edge-behållaren är mappad till. Om t. ex. IP-adressen för Docker-värden är *xxx.xxx.xxx.xxx* och Azure SQL Edge-behållaren mappas till värd port *1600*, skulle Server adressen för instansen av Azure SQL Edge vara *xxx. xxx. xxx. xxx, 1600*. Det uppdaterade python-skriptet är:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Om du vill ansluta till en instans av Azure SQL Edge genom att använda SQL Server Management Studio som körs på en Windows-dator, se [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Information om hur du ansluter till en instans av Azure SQL Edge med hjälp av Visual Studio Code på en Windows-, Mac-eller Linux-dator finns i [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Om du vill ansluta till en instans av Azure SQL Edge genom att använda Azure Data Studio på en Windows-, Mac-eller Linux-dator, se [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Nästa steg

[Anslut och fråga](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Installera SQL Server verktyg på Linux](/sql/linux/sql-server-linux-setup-tools)