---
title: Krav för Azure Database Migration Service
description: Läs om en översikt över kraven för att använda Azure Database Migration Service för att utföra migrering av databasen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094052"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Översikt över förutsättningar för att använda Azure Database Migration Service

Det krävs flera krav för att säkerställa Azure Database Migration Service fungerar smidigt när du utför databas migreringar. Vissa krav gäller för alla scenarier (käll mål par) som stöds av tjänsten, medan andra krav är unika för ett visst scenario.

Krav som är kopplade till med hjälp av Azure Database Migration Service visas i följande avsnitt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Krav som är vanliga i scenarier med migrering

Azure Database Migration Service förutsättningar som är gemensamma för alla typer av migrering som stöds omfattar behovet av att:

* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Se till att dina regler för nätverks säkerhets gruppen (NSG) för virtuella nätverk inte blockerar den utgående porten 443 för ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Att skapa en instans av Azure Database Migration Service kräver åtkomst till inställningarna för virtuella nätverk som normalt inte ingår i samma resurs grupp. Det innebär att användaren som skapar en instans av DMS kräver behörighet på prenumerations nivå. Om du vill skapa de nödvändiga rollerna, som du kan tilldela vid behov, kör du följande skript:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Förutsättningar för att migrera SQL Server till Azure SQL Database

Förutom att Azure Database Migration Service krav som är gemensamma för alla migreringsåtgärder, finns det också krav som gäller specifikt för ett scenario eller ett annat.

När du använder Azure Database Migration Service för att utföra SQL Server till Azure SQL Database migreringar, förutom de krav som är gemensamma för alla migreringsåtgärder, måste du ta itu med följande ytterligare krav:

* Skapa en instans av Azure SQL Database-instansen, som du gör genom att följa detalj i artikeln [skapa en databas i Azure SQL Database i Azure Portal](../azure-sql/database/single-database-create-quickstart.md).
* Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) version 3.3 eller senare.
* Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Skapa en [brand Väggs regel](../azure-sql/database/firewall-configure.md) på server nivå för SQL Database för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
* Se till att de autentiseringsuppgifter som används för att ansluta till mål databasen har behörighet att kontrol lera databasen på mål databasen.

   > [!NOTE]
   > En fullständig lista över de krav som krävs för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till Azure SQL Database finns i självstudien [migrera SQL Server till Azure SQL Database](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Krav för migrering av SQL Server till en Azure SQL-hanterad instans

* Skapa en SQL-hanterad instans genom att följa informationen i artikeln [skapa en hanterad Azure SQL-instans i Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Öppna brand väggarna för att tillåta SMB-trafik på port 445 för den Azure Database Migration Service IP-adressen eller under nätets intervall.
* Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
* Skapa en nätverksresurs som Azure Database Migration Service kan använda till att säkerhetskopiera källdatabasen.
* Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
* Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage behållare för återställnings åtgärden.
* Skapa en BLOB-behållare och hämta dess SAS-URI genom att följa stegen i artikeln [Hantera Azure Blob Storage-resurser med Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Se till att välja alla behörigheter (läsa, skriva, ta bort, lista) i princip fönstret när du skapar SAS-URI: n.

   > [!NOTE]
   > En fullständig lista över de krav som krävs för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till SQL-hanterad instans finns i självstudien [migrera SQL Server till SQL-hanterad instans](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Azure Database migration service](dms-overview.md).