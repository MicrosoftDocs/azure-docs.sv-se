---
title: Importera eller exportera en Azure SQL Database utan att ge Azure-tjänster åtkomst till servern.
description: Importera eller exportera en Azure SQL Database utan att ge Azure-tjänster åtkomst till servern.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724177"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importera eller exportera en Azure SQL Database utan att ge Azure-tjänster åtkomst till servern
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs hur du importerar eller exporterar en Azure SQL Database när *Tillåt att Azure-tjänster* är inställt på på servern.  Arbets flödet använder en virtuell Azure-dator för att köra SqlPackage för att utföra import-eller export åtgärden.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Skapa den virtuella Azure-datorn

Skapa en virtuell Azure-dator genom att välja knappen **distribuera till Azure** .

Med den här mallen kan du distribuera en enkel virtuell Windows-dator med några olika alternativ för Windows-versionen med den senaste versionen som korrigeras. Detta distribuerar en virtuell dator i A2-storlek i resurs gruppens plats och returnerar det fullständigt kvalificerade domän namnet för den virtuella datorn.
<br><br>

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Mer information finns i [enkel distribution av en virtuell Windows-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till den virtuella datorn via en fjärr skrivbords anslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

   ![Skärm bild som visar en översikts sida för virtuell dator med knappen Anslut.](./media/database-import-export-azure-services-off/vm.png)  

2. Välj **Anslut**.

   En Remote Desktop Protocol fil (. RDP-fil) visas med den offentliga IP-adressen och port numret för den virtuella datorn.

   ![RDP-formulär](./media/database-import-export-azure-services-off/rdp.png)  

3. Välj **Hämta RDP-fil**.

   > [!NOTE]
   > Du kan också använda SSH för att ansluta till din virtuella dator.

4. Stäng formuläret **Anslut till virtuell dator** .
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn.
6. När du uppmanas väljer du **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) från Mac App Store.

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn och välj sedan **OK**.

8. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="install-sqlpackage"></a>Installera SqlPackage

[Hämta och installera den senaste versionen av SqlPackage](/sql/tools/sqlpackage-download).

Mer information finns i [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Skapa en brand Väggs regel för att tillåta VM-åtkomst till databasen

Lägg till den virtuella datorns offentliga IP-adress i serverns brand vägg.

Följande steg skapar en IP-brandväggsregel på server nivå för den virtuella datorns offentliga IP-adress och möjliggör anslutning från den virtuella datorn.

1. Välj **SQL-databaser** på den vänstra menyn och välj sedan databasen på sidan SQL- **databaser** . Översikts sidan för databasen öppnas och visar det fullständigt kvalificerade Server namnet (till exempel **servername.Database.Windows.net**) och alternativ för ytterligare konfiguration.

2. Kopiera det här fullständigt kvalificerade Server namnet som ska användas när du ansluter till servern och dess databaser.

   ![servernamn](./media/database-import-export-azure-services-off/server-name.png)

3. Välj **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **brand Väggs inställningar** för servern öppnas.

   ![IP-brandväggsregel på servernivå](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Välj **Lägg till klient-IP** i verktygsfältet för att lägga till den virtuella datorns offentliga IP-adress till en ny regel för IP-brandvägg på server nivå. Med en IP-brandväggsregel på servernivå kan du öppna port 1433 för en enskild IP-adress eller för ett IP-adressintervall.

5. Välj **Spara**. En regel för IP-brandvägg på server nivå skapas för den virtuella datorns offentliga IP-adress som öppnar port 1433 på servern.

6. Stäng sidan **Brandväggsinställningar**.

## <a name="export-a-database-using-sqlpackage"></a>Exportera en databas med SqlPackage

Om du vill exportera en Azure SQL Database med hjälp av kommando rads verktyget [SqlPackage](/sql/tools/sqlpackage) , se [Exportera parametrar och egenskaper](/sql/tools/sqlpackage#export-parameters-and-properties). SqlPackage-verktyget levereras med de senaste versionerna av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt), eller så kan du ladda ned den senaste versionen av [SqlPackage](/sql/tools/sqlpackage-download).

Vi rekommenderar att du använder SqlPackage-verktyget för skalning och prestanda i de flesta produktions miljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage.exe med Active Directory Universal Authentication. Ersätt med värden som är speciella för din miljö.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Importera en databas med SqlPackage

Om du vill importera en SQL Server-databas med hjälp av kommando rads verktyget [SqlPackage](/sql/tools/sqlpackage) , se [import parametrar och egenskaper](/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har den senaste [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt). Du kan också hämta den senaste versionen av [SqlPackage](/sql/tools/sqlpackage-download).

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktions miljöer snarare än att använda Azure Portal. En SQL Server kund expert team blogg om hur du migrerar med hjälp av `BACPAC` filer finns i [migrera från SQL Server till Azure SQL Database använda BACPAC-filer](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Följande SqlPackage-kommando importerar **AdventureWorks2017** -databasen från lokal lagring till en Azure SQL Database. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium** service-nivå och ett **P6** -tjänst mål. Ändra värdena efter behov för din miljö.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill ansluta till tAzure SQL Database från en företags brand vägg måste brand väggen ha port 1433 öppen.

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Export hastigheten varierar på grund av många faktorer (till exempel data form) så det är omöjligt att förutsäga vilken hastighet som ska förväntas. SqlPackage kan ta lång tid, särskilt för stora databaser.

För att få bästa möjliga prestanda kan du prova följande strategier:

1. Se till att inga andra arbetsbelastningar körs på databasen. Skapa en kopia innan exporten kan vara den bästa lösningen för att se till att inga andra arbets belastningar körs.
2. Öka service nivå målet för databasen (service nivå mål) för att bättre hantera export belastningen (I huvudsak Läs-I/O). Om databasen för närvarande GP_Gen5_4 kan en Affärskritisk nivå hjälpa till med Läs-och skriv arbets belastning.
3. Se till att det finns grupperade index särskilt för stora tabeller.
4. Virtual Machines (VM) måste finnas i samma region som databasen för att hjälpa till att undvika nätverks begränsningar.
5. Virtuella datorer ska ha SSD med tillräckligt stor storlek för att generera tillfälliga artefakter innan överföring till blob-lagring.
6. Virtuella datorer bör ha tillräcklig Core-och minnes konfiguration för den aktuella databasen.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Lagra importerade eller exporterade. BACPAC-fil

Det. BACPAC-filen kan lagras i [Azure-blobbar](../../storage/blobs/storage-blobs-overview.md)eller [Azure Files](../../storage/files/storage-files-introduction.md).

Använd Azure Files om du vill uppnå bästa prestanda. SqlPackage fungerar med fil systemet så att det kan komma åt Azure Files direkt.

Om du vill minska kostnaderna använder du Azure-blobbar som är mindre än en Premium Azure-filresurs. Du måste dock kopiera [. BACPAC-filen](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) mellan blobben och det lokala fil systemet före import-eller export åtgärden. Det innebär att processen tar längre tid.

För att ladda upp eller ladda ned. BACPAC-filer finns i [överföra data med AzCopy och Blob Storage](../../storage/common/storage-use-azcopy-v10.md#transfer-data)och [överföra data med AzCopy och fil lagring](../../storage/common/storage-use-azcopy-files.md).

Beroende på din miljö kan du behöva [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du ansluter till och frågar en importerad SQL Database finns i [snabb start: Azure SQL Database: använd SQL Server Management Studio för att ansluta och fråga efter data](connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En beskrivning av hela processen för migrering av SQL Server databasen, inklusive prestanda rekommendationer, finns i [SQL Server Database migration till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om hur du hanterar och delar lagrings nycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security guide](../../storage/blobs/security-recommendations.md).
