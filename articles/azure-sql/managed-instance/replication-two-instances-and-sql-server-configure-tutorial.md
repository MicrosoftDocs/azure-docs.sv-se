---
title: Konfigurera Transaktionsreplikering mellan Azure SQL-hanterad instans och SQL Server
description: En själv studie kurs som konfigurerar replikering mellan en hanterad utgivar instans, en distributörs hanterad instans och en SQL Server prenumerant på en virtuell Azure-dator, tillsammans med nödvändiga nätverks komponenter som privat DNS-zon och VNet-peering.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593934"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Självstudie: Konfigurera Transaktionsreplikering mellan Azure SQL-hanterad instans och SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Med Transaktionsreplikering kan du replikera data från en databas till en annan som finns på antingen SQL Server eller [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md). SQL-hanterad instans kan vara utgivare, distributör eller prenumerant i replikeringstopologin. Se [konfigurationer för transaktionell replikering](replication-transactional-overview.md#common-configurations) för tillgängliga konfigurationer. 

Transaktionell replikering är för närvarande en offentlig för hands version för SQL-hanterad instans. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Konfigurera en hanterad instans som en replik utgivare.
> - Konfigurera en hanterad instans som en replikerings-distributör.
> - Konfigurera SQL Server som prenumerant.

![Replikering mellan en hanterad instans utgivare, en hanterad instans distributör och SQL Server prenumerant](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Den här självstudien är avsedd för en erfaren mål grupp och förutsätter att användaren är van vid distribution och anslutning till både hanterade instanser och SQL Server virtuella datorer i Azure. 


> [!NOTE]
> I den här artikeln beskrivs användningen av [transaktionell replikering](/sql/relational-databases/replication/transactional/transactional-replication) i Azure SQL-hanterad instans. Den är inte relaterad till [redundansväxlingen](../database/auto-failover-group-overview.md), en funktion för Azure SQL-hanterad instans som gör att du kan skapa kompletta läsbara repliker av enskilda instanser. Det finns ytterligare överväganden när du konfigurerar [transaktionell replikering med grupper för växling vid fel](replication-transactional-overview.md#with-failover-groups).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- Erfarenhet av distribution av två hanterade instanser i samma virtuella nätverk.
- En SQL Server prenumerant, antingen lokalt eller på en virtuell Azure-dator. I den här självstudien används en virtuell Azure-dator.  
- [SQL Server Management Studio (SSMS) 18,0 eller senare](/sql/ssms/download-sql-server-management-studio-ssms).
- Den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps).
- Portarna 445 och 1433 tillåter SQL-trafik både i Azure-brandväggen och Windows-brandväggen.

## <a name="create-the-resource-group"></a>Skapa resursgruppen

Använd följande PowerShell-kodfragment för att skapa en ny resurs grupp:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Skapa två hanterade instanser

Skapa två hanterade instanser i den här nya resurs gruppen med hjälp av [Azure Portal](https://portal.azure.com).

- Namnet på den hanterade instansen av utgivare ska vara `sql-mi-publisher` (tillsammans med några tecken för slumpmässigheten) och namnet på det virtuella nätverket ska vara `vnet-sql-mi-publisher` .
- Namnet på den hanterade instansen av distributören ska vara `sql-mi-distributor` (tillsammans med några tecken för slumpmässigheten) och bör vara _i samma virtuella nätverk som utgivarens hanterade instans_.

   ![Använda Publisher VNet för distributören](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Mer information om hur du skapar en hanterad instans finns i [skapa en hanterad instans i portalen](instance-create-quickstart.md).

  > [!NOTE]
  > För enkelhetens skull, och eftersom det är den vanligaste konfigurationen, föreslår den här självstudien placeringen av den hanterade instansen av distributör i samma virtuella nätverk som utgivaren. Det är dock möjligt att skapa distributören i ett separat virtuellt nätverk. För att göra det måste du konfigurera VNet-peering mellan virtuella nätverk för utgivaren och distributören och sedan konfigurera VNet-peering mellan distributörens virtuella nätverk och prenumerant.

## <a name="create-a-sql-server-vm"></a>Skapa en SQL Server VM

Skapa en virtuell SQL Server virtuell dator med hjälp av [Azure Portal](https://portal.azure.com). Den SQL Server virtuella datorn måste ha följande egenskaper:

- Namn: `sql-vm-sub`
- Bild: SQL Server 2016 eller mer
- Resurs grupp: samma som den hanterade instansen
- Virtuellt nätverk: `sql-vm-sub-vnet`

Mer information om hur du distribuerar en SQL Server VM till Azure finns i [snabb start: skapa en SQL Server VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Konfigurera VNet-peering

Konfigurera VNet-peering för att aktivera kommunikation mellan det virtuella nätverket för de två hanterade instanserna och det virtuella nätverket för SQL Server. Det gör du genom att använda det här PowerShell-kodfragmentet:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

När VNet-peering har upprättats kan du testa anslutningen genom att starta SQL Server Management Studio (SSMS) på SQL Server och ansluta till båda hanterade instanserna. Mer information om hur du ansluter till en hanterad instans med hjälp av SSMS finns i [använda SSMS för att ansluta till SQL-hanterad instans](point-to-site-p2s-configure.md#connect-with-ssms).

![Testa anslutningen till de hanterade instanserna](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

En privat DNS-zon tillåter DNS-routning mellan de hanterade instanserna och SQL Server.

### <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **skapa en resurs** för att skapa en ny Azure-resurs.
1. Sök `private dns zone` på Azure Marketplace.
1. Välj den **privat DNS zon** resurs som publicerats av Microsoft och välj sedan **skapa** för att skapa DNS-zonen.
1. Välj prenumerationen och resurs gruppen i list rutan.
1. Ange ett godtyckligt namn för DNS-zonen, till exempel `repldns.com` .

   ![Skapa en privat DNS-zon](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Välj **Granska + skapa**. Granska parametrarna för din privata DNS-zon och välj **skapa** för att skapa din resurs.

### <a name="create-an-a-record"></a>Skapa en A-post

1. Gå till din nya **privat DNS zon** och välj **Översikt**.
1. Välj **+ post uppsättning** för att skapa en ny post.
1. Ange namnet på din SQL Server VM samt den privata interna IP-adressen.

   ![Konfigurera en A-post](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Välj **OK** för att skapa en post.

### <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

1. Gå till din nya **privat DNS zon** och välj **länkar till virtuella nätverk**.
1. Välj **+ Lägg till**.
1. Ange ett namn för länken, till exempel `Pub-link` .
1. Välj din prenumeration i list rutan och välj sedan det virtuella nätverket för utgivarens hanterade instans.
1. Markera kryss rutan bredvid **Aktivera automatisk registrering**.

   ![Skapa VNet-länk](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Välj **OK** för att länka ditt virtuella nätverk.
1. Upprepa de här stegen för att lägga till en länk till det virtuella prenumeranten för prenumerant, med ett namn som `Sub-link` .

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

[Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md#create-a-storage-account) för arbets katalogen och skapa sedan en [fil resurs](../../storage/files/storage-how-to-create-file-share.md) i lagrings kontot.

Kopiera sökvägen till fil resursen i formatet: `\\storage-account-name.file.core.windows.net\file-share-name`

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera anslutnings strängen för lagrings åtkomst nyckeln i formatet: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Skapa en databas

Skapa en ny databas på utgivarens hanterade instans. Det gör du på följande sätt:

1. Starta SQL Server Management Studio på SQL Server.
1. Anslut till den `sql-mi-publisher` hanterade instansen.
1. Öppna ett **nytt frågefönster** och kör följande T-SQL-fråga för att skapa databasen.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Konfigurera distribution

När anslutningen har upprättats och du har en exempel databas kan du konfigurera distributionen på din `sql-mi-distributor` hanterade instans. Det gör du på följande sätt:

1. Starta SQL Server Management Studio på SQL Server.
1. Anslut till den `sql-mi-distributor` hanterade instansen.
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att konfigurera distributionen på den hanterade distributörs instansen:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Se till att endast använda omvända snedstreck ( `\` ) för @working_directory parametern. Om du använder ett snedstreck ( `/` ) kan det orsaka ett fel när du ansluter till fil resursen.

1. Anslut till den `sql-mi-publisher` hanterade instansen.
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att registrera distributören på utgivaren:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Skapa publikationen

När distributionen har kon figurer ATS kan du nu skapa publikationen. Det gör du på följande sätt:

1. Starta SQL Server Management Studio på SQL Server.
1. Anslut till den `sql-mi-publisher` hanterade instansen.
1. I **Object Explorer** expanderar du noden **replikering** och högerklickar på mappen för den **lokala publikationen** . Välj **ny publikation...**.
1. Välj **Nästa** för att gå förbi Välkomst sidan.
1. På sidan **publicerings databas** väljer du den `ReplTutorial` databas du skapade tidigare. Välj **Nästa**.
1. På sidan **publikationstyp** väljer du **transaktionell publikation**. Välj **Nästa**.
1. Markera kryss rutan bredvid **tabeller** på sidan **artiklar** . Välj **Nästa**.
1. På sidan **filtrera tabell rader** väljer du **Nästa** utan att lägga till några filter.
1. På sidan **ögonblicks bild agent** markerar du kryss rutan bredvid **skapa ögonblicks bild direkt och håller ögonblicks bilden tillgänglig för att initiera prenumerationer**. Välj **Nästa**.
1. På sidan **agent säkerhet** väljer du **säkerhets inställningar...**. Ange SQL Server inloggnings uppgifter som ska användas för ögonblicks bild agenten och för att ansluta till utgivaren. Välj **OK** för att stänga **säkerhets sidan för ögonblicks bild agent** . Välj **Nästa**.

   ![Konfigurera säkerhet för ögonblicks bild agent](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. På sidan **åtgärder i guiden** väljer du att **skapa publikationen** och (valfritt) Välj för att **Generera en skript fil med steg för att skapa publikationen** om du vill spara skriptet till senare.
1. På sidan **Slutför guiden** namnger du publikationen `ReplTest` och väljer **Nästa** för att skapa publikationen.
1. När publikationen har skapats uppdaterar du noden **replikering** i **Object Explorer** och expanderar **lokala publikationer** för att se din nya publikation.

## <a name="create-the-subscription"></a>Skapa prenumerationen

När publikationen har skapats kan du skapa prenumerationen. Det gör du på följande sätt:

1. Starta SQL Server Management Studio på SQL Server.
1. Anslut till den `sql-mi-publisher` hanterade instansen.
1. Öppna ett **nytt frågefönster** och kör följande Transact-SQL-kod för att lägga till prenumerations-och distributions agenten. Använd DNS som en del av namnet på prenumeranten.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Testa replikering

När replikeringen har kon figurer ATS kan du testa den genom att infoga nya objekt i utgivaren och titta på ändringarna Sprid till prenumeranten.

Kör följande T-SQL-kodfragment för att visa raderna på prenumeranten:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Kör följande T-SQL-kodfragment för att infoga ytterligare rader i utgivaren och kontrol lera sedan raderna igen på prenumeranten.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com).
1. Välj de hanterade instanserna och välj sedan **ta bort**. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden och tills den är klar kommer du inte att kunna ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken **aktivitet** för att bekräfta att den hanterade instansen har tagits bort.
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resurs gruppen och sedan välja **ta bort**. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**.
1. Ta bort eventuella återstående resurser. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**.
1. Ta bort resurs gruppen genom att välja **ta bort resurs** grupp, Skriv namnet på resurs gruppen `myResourceGroup` och välj sedan **ta bort**.

## <a name="known-errors"></a>Kända fel

### <a name="windows-logins-are-not-supported"></a>Windows-inloggningar stöds inte

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agenten konfigurerades med en Windows-inloggning och måste använda en SQL Server inloggning i stället. Använd sidan **agent säkerhet** i **publicerings egenskaperna** för att ändra inloggnings uppgifterna till en SQL Server inloggning.

### <a name="failed-to-connect-to-azure-storage"></a>Det gick inte att ansluta till Azure Storage

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 erhöll Azure Storage anslutnings sträng för replstorage 2019-11-19 02:21:05.07 ansluter till Azure Files Storage ' \\ replstorage. File. Core. Windows. net\replshare ' 2019-11-19 02:21:31.21 kunde inte ansluta till Azure Storage ' med OS-fel: 53.

Detta beror förmodligen på att port 445 är stängd i antingen Azure-brandväggen, Windows-brandväggen eller både och.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Om du använder ett snedstreck i stället för omvänt snedstreck i fil resursens fil Sök väg kan detta fel uppstå.
  
  - Detta är OK: `\\replstorage.file.core.windows.net\replshare`
  - Detta kan orsaka ett OS 55-fel: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Det gick inte att ansluta till prenumeranten

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Möjliga lösningar:

- Se till att port 1433 är öppen.
- Se till att TCP/IP är aktiverat på prenumeranten.
- Bekräfta att DNS-namnet användes när prenumeranten skapades.
- Kontrol lera att de virtuella nätverken är korrekt länkade i den privata DNS-zonen.
- Verifiera att en post är korrekt konfigurerad.
- Kontrol lera att din VNet-peering är korrekt konfigurerad.

### <a name="no-publications-to-which-you-can-subscribe"></a>Det går inte att prenumerera på några publikationer

När du lägger till en ny prenumeration med hjälp av guiden **ny prenumeration** kan du se att det inte finns några databaser och publikationer som är tillgängliga på sidan **publikation** , och du kan se följande fel meddelande:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Även om det är möjligt att det här fel meddelandet är korrekt och det inte finns publikationer som är tillgängliga på utgivaren som du har anslutit till, eller om du saknar tillräckliga behörigheter, kan det här felet också orsakas av en äldre version av SQL Server Management Studio. Försök att uppgradera till SQL Server Management Studio 18,0 eller mer för att utesluta detta som en rotorsak.

## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Se artikeln [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) för en omfattande lista över olika sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [SQL-hanterad instans granskning](auditing-configure.md)
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](threat-detection-configure.md)
- [Dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security)
- [Transparent data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL-hanterade instans funktioner

En fullständig översikt över funktioner för hanterade instanser finns i:

> [!div class="nextstepaction"]
> [SQL-hanterade instans funktioner](sql-managed-instance-paas-overview.md)