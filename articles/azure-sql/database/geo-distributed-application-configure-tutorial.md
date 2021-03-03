---
title: Implementera en geo-distribuerad lösning
description: Lär dig hur du konfigurerar databasen i Azure SQL Database-och klient program för redundansväxling till en replikerad databas och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 89d285a56553f5c521d1edbc92786debd4a92e32
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659298"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Självstudie: implementera en geo-distribuerad databas (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfigurera en databas i SQL Database och klient program för redundansväxling till en fjärrregion och testa en redundanskluster. Lär dig att:

> [!div class="checklist"]
>
> - Skapa en [grupp för redundans](auto-failover-group-overview.md)
> - Köra ett Java-program för att fråga en databas i SQL Database
> - Redundanstest

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Kontrol lera att du har installerat följande objekt för att slutföra självstudien:

- [Azure PowerShell](/powershell/azure/)
- En enda databas i Azure SQL Database. För att skapa en användning,
  - [Azure Portal](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > I självstudien används exempel databasen *AdventureWorksLT* .

- Java och maven, se [Bygg en app med SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), markera **Java** och välj din miljö och följ sedan stegen.

> [!IMPORTANT]
> Se till att konfigurera brand Väggs regler för att använda den offentliga IP-adressen för den dator där du utför stegen i den här självstudien. Brand Väggs regler på databas nivå kommer att replikeras automatiskt till den sekundära servern.
>
> Mer information finns i skapa en brand [Väggs regel på databas nivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) eller för att fastställa vilken IP-adress som används för brand Väggs regeln på server nivå för datorn se [skapa en brand vägg på server nivå](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Skapa en grupp för redundans

Med hjälp av Azure PowerShell skapar du [Redundansrelationer](auto-failover-group-overview.md) mellan en befintlig server och en ny server i en annan region. Lägg sedan till exempel databasen i gruppen för växling vid fel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Om du vill skapa en grupp för växling vid fel kör du följande skript:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Kör `az login` för att logga in på Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Inställningar för geo-replikering kan också ändras i Azure Portal, genom att välja din databas och **Inställningar**  >  **geo-replikering**.

![Inställningar för geo-replikering](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Kör exempelprojektet

1. I-konsolen skapar du ett Maven-projekt med följande kommando:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Skriv in **Y** och tryck på **RETUR**.

1. Ändra kataloger till det nya projektet.

   ```bash
   cd SqlDbSample
   ```

1. Öppna *pom.xml* -filen i projektmappen med hjälp av din favorit redigerare.

1. Lägg till Microsoft JDBC-drivrutinen för SQL Server beroende genom att lägga till följande `dependency` avsnitt. Beroendet måste klistras in i det större `dependencies` avsnittet.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Ange Java-versionen genom att lägga till `properties` avsnittet efter `dependencies` avsnittet:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Stöd MANIFEST filer genom att lägga till `build` avsnittet efter `properties` avsnittet:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Spara och Stäng *pom.xmls* filen.

1. Öppna filen *app. java* som finns i.. \SqlDbSample\src\main\java\com\sqldbsamples och ersätt innehållet med följande kod:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i)) ? "successful" : "failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i)) ? "successful" : "failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Spara och Stäng filen *app. java* .

1. Kör följande kommando i kommando konsolen:

   ```bash
   mvn package
   ```

1. Starta programmet som ska köras i ungefär 1 timme tills det stoppas manuellt, så att du kan köra redundanstestning igen.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Redundanstest

Kör följande skript för att simulera en redundansväxling och observera program resultatet. Observera att det inte går att utföra vissa infogningar och val under migreringen av databasen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan kontrol lera rollen för haveri återställnings servern under testet med följande kommando:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Så här testar du en redundansväxling:

1. Starta en manuell redundansväxling av gruppen för redundans:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Återställ redundansväxlingen tillbaka till den primära servern:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan kontrol lera rollen för haveri återställnings servern under testet med följande kommando:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Så här testar du en redundansväxling:

1. Starta en manuell redundansväxling av gruppen för redundans:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Återställ redundansväxlingen tillbaka till den primära servern:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en databas i Azure SQL Database och ett program för redundansväxling till en fjärrregion och testade en schema för redundans. Du har lärt dig att:

> [!div class="checklist"]
>
> - Skapa en redundansgrupp för geo-replikering
> - Köra ett Java-program för att fråga en databas i SQL Database
> - Redundanstest

Gå vidare till nästa självstudie om hur du lägger till en instans av en Azure SQL-hanterad instans i en failover-grupp:

> [!div class="nextstepaction"]
> [Lägga till en instans av en hanterad Azure SQL-instans i en grupp för redundans](../managed-instance/failover-group-add-instance-tutorial.md)
