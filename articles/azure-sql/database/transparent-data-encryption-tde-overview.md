---
title: Transparent datakryptering
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: En översikt över transparent datakryptering för Azure SQL Database, Azure SQL Managed Instance och Azure Synapse Analytics. Dokumentet beskriver dess fördelar och konfigurationsalternativ, bland annat tjänst-hanterad transparent datakryptering och Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 160066f9599388256c7c821732a1e06fec49bdf5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749050"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent datakryptering för SQL Database, SQL Managed Instance och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) hjälper till att skydda Azure SQL Database, Azure SQL Managed Instance och Azure Synapse Analytics mot hot om skadlig offlineaktivitet genom att kryptera vilodata. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade SQL-databaser och måste aktiveras manuellt för äldre databaser med Azure SQL Database, Azure SQL Managed Instance. TDE måste aktiveras manuellt för Azure Synapse Analytics.

TDE utför I/O-kryptering i realtid och dekryptering av data på sidnivå. Varje sida dekrypteras när de läses in i minnet och krypteras sedan innan de skrivs tillbaka till disken. TDE krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas Databaskrypteringsnyckel (DEK). Vid databasstart dekrypteras den krypterade DEK:n och används sedan för dekryptering och omkryptering av databasfilerna SQL Server databasmotorn. DEK skyddas av TDE-skyddet. TDE-skydd är antingen ett tjänst hanterat certifikat (tjänst-hanterad transparent datakryptering) eller en asymmetrisk nyckel som lagras [i Azure Key Vault](../../key-vault/general/security-overview.md) (kund-hanterad transparent datakryptering).

För Azure SQL Database och Azure Synapse ärvs TDE-skyddet på [servernivå](logical-servers.md) av alla databaser som är associerade med den servern. Till Azure SQL Managed Instance ärvs TDE-skyddet på instansnivå och ärvs av alla krypterade databaser på den instansen. Termen server *refererar* både till server och instans i hela det här dokumentet, om inget annat anges.

> [!IMPORTANT]
> Alla nyligen skapade databaser i SQL Database krypteras som standard med hjälp av tjänst-hanterad transparent datakryptering. Befintliga SQL-databaser som skapats före maj 2017 och SQL-databaser som skapats via återställning, geo-replikering och databaskopiering krypteras inte som standard. Befintliga SQL Managed Instance som skapats före februari 2019 krypteras inte som standard. SQL Managed Instance databaser som skapats via återställning ärver krypteringsstatus från källan.

> [!NOTE]
> TDE kan inte användas för att  kryptera systemdatabaser, till exempel huvuddatabasen, i Azure SQL Database och Azure SQL Managed Instance. **Huvuddatabasen** innehåller objekt som behövs för att utföra TDE-åtgärderna på användardatabaserna. Vi rekommenderar att du inte lagrar känsliga data i systemdatabaserna. [Infrastrukturkryptering](transparent-data-encryption-byok-overview.md#doubleencryption) distribueras nu, vilket krypterar systemdatabaserna inklusive huvuddatabaserna. 

## <a name="service-managed-transparent-data-encryption"></a>Tjänst-hanterad transparent datakryptering

I Azure är standardinställningen för TDE att DEK skyddas av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje server och krypteringsalgoritmen som används är AES 256. Om en databas finns i en geo-replikeringsrelation skyddas både den primära och den geo-sekundära databasen av den primära databasens överordnade servernyckel. Om två databaser är anslutna till samma server delar de också samma inbyggda certifikat. Microsoft roterar automatiskt dessa certifikat i enlighet med den interna säkerhetsprincipen och rotnyckeln skyddas av ett internt Microsoft-hemlighetsarkiv. Kunder kan kontrollera SQL Database och SQL Managed Instance med interna säkerhetsprinciper i oberoende granskningsrapporter från tredje part som är tillgängliga i [Microsoft Trust Center.](https://servicetrust.microsoft.com/)

Microsoft flyttar och hanterar även sömlöst nycklarna efter behov för geo-replikering och återställningar.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Kund hanterad transparent datakryptering – Bring Your Own Key

Kund-hanterad TDE kallas även för byok-stöd (Bring Your Own Key) för TDE. I det här scenariot är TDE-skyddet som krypterar DEK en kund hanterad asymmetrisk nyckel som lagras i en kundägd och hanterad Azure Key Vault (Azures molnbaserade externa nyckelhanteringssystem) och lämnar aldrig nyckelvalvet. TDE-skyddet kan [genereras av](../../key-vault/keys/hsm-protected-keys.md) nyckelvalvet eller överföras till nyckelvalvet från en lokal maskinvarusäkerhetsmodul (HSM). SQL Database, SQL Managed Instance och Azure Synapse måste beviljas behörigheter till det kundägda nyckelvalvet för att dekryptera och kryptera DEK. Om serverns behörigheter till nyckelvalvet återkallas är en databas otillgänglig och alla data krypteras

Med TDE med Azure Key Vault-integrering kan användare styra nyckelhanteringsuppgifter som nyckelrotation, behörigheter för nyckelvalv, nyckelsäkerhetskopior och aktivera granskning/rapportering av alla TDE-skydd med hjälp av Azure Key Vault funktioner. Key Vault ger central nyckelhantering, använder noggrant övervakade HSM:er och möjliggör uppdelning av uppgifter mellan hantering av nycklar och data för att uppfylla efterlevnad av säkerhetsprinciper.
Mer information om BYOK för Azure SQL Database och Azure Synapse finns i [Transparent datakryptering med Azure Key Vault integrering](transparent-data-encryption-byok-overview.md).

Om du vill börja använda transparent datakryptering Azure Key Vault integreringen kan du gå till i guiden Aktivera transparent datakryptering med hjälp av din egen [nyckel från Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent datakrypteringsskyddad databas

Du behöver inte dekryptera databaser för åtgärder i Azure. TDE-inställningarna för källdatabasen eller den primära databasen ärvs transparent på målet. Åtgärder som ingår omfattar:

- Geo-återställning
- Återställning till tidpunkt med självbetjäning
- Återställning av en borttagna databas
- Aktiv geo-replikering
- Skapa en databaskopia
- Återställa säkerhetskopian till Azure SQL Managed Instance

> [!IMPORTANT]
> Det går inte att ta manuell säkerhetskopiering med endast kopiering av en databas som krypterats av tjänst-hanterad TDE i Azure SQL Managed Instance, eftersom det certifikat som används för kryptering inte är tillgängligt. Använd funktionen för återställning till tidpunkt för att flytta den här typen av databas till en annan SQL Managed Instance eller växla till kund hanterad nyckel.

När du exporterar en TDE-skyddad databas krypteras inte det exporterade innehållet i databasen. Det här exporterade innehållet lagras i okrypterade BACPAC-filer. Se till att skydda BACPAC-filerna på rätt sätt och aktivera TDE när importen av den nya databasen är klar.

Om BACPAC-filen till exempel exporteras från en SQL Server-instans krypteras inte det importerade innehållet i den nya databasen automatiskt. Om BACPAC-filen importeras till en SQL Server-instans krypteras inte heller den nya databasen automatiskt.

Det enda undantaget är när du exporterar en databas till och från SQL Database. TDE är aktiverat på den nya databasen, men själva BACPAC-filen är fortfarande inte krypterad.

## <a name="manage-transparent-data-encryption"></a>Hantera transparent datakryptering

# <a name="the-azure-portal"></a>[Azure-portalen](#tab/azure-portal)

Hantera TDE i Azure Portal.

För att konfigurera TDE via Azure Portal måste du vara ansluten som Azure-ägare, deltagare eller SQL-säkerhetshanterare.

Aktivera och inaktivera TDE på databasnivå. Använd Azure SQL Managed Instance Transact-SQL (T-SQL) för att aktivera och inaktivera TDE på en databas. För Azure SQL Database och Azure Synapse kan du hantera TDE för databasen i [Azure Portal](https://portal.azure.com) när du har loggat in med Azure-administratörs- eller deltagarkontot. Hitta TDE-inställningarna under användardatabasen. Som standard används tjänst-hanterad transparent datakryptering. Ett TDE-certifikat genereras automatiskt för den server som innehåller databasen.

![Tjänst hanterad transparent datakryptering](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Du anger TDE-huvudnyckeln, som kallas TDE-skydd, på server- eller instansnivå. Om du vill använda TDE med BYOK-stöd och skydda dina databaser med en nyckel från Key Vault öppnar du TDE-inställningarna under servern.

![Transparent datakryptering med Bring Your Own Key stöd](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hantera TDE med hjälp av PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande, men all framtida utveckling är till för Az.Sql-modulen. Dessa cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska.

Om du vill konfigurera TDE via PowerShell måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets för Azure SQL Database och Azure Synapse

Använd följande cmdlets för Azure SQL Database och Azure Synapse:

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent datakryptering för en databas.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hämtar transparent datakrypteringstillstånd för en databas. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontrollerar krypteringsförloppet för en databas. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Lägger till Key Vault nyckel till en server. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hämtar Key Vault för en server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Anger transparent datakrypteringsskydd för en server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hämtar skyddet för transparent datakryptering |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Tar bort en Key Vault nyckel från en server. |
|  | |

> [!IMPORTANT]
> Du Azure SQL Managed Instance T-SQL [ALTER DATABASE-kommandot](/sql/t-sql/statements/alter-database-azure-sql-database) för att aktivera och inaktivera TDE på databasnivå och kontrollera [PowerShell-exempelskriptet](transparent-data-encryption-byok-configure.md) för att hantera TDE på instansnivå.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Hantera TDE med hjälp av Transact-SQL.

Anslut till databasen med en inloggning som är administratör eller medlem i **dbmanager-rollen** i huvuddatabasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | ANGE KRYPTERING PÅ/AV krypterar eller dekrypterar en databas |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypteringstillståndet för en databas och dess associerade databaskrypteringsnycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypteringstillståndet för varje Azure Synapse och dess associerade databaskrypteringsnycklar |
|  | |

Du kan inte växla TDE-skydd till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure Portal.

# <a name="rest-api"></a>[REST-API](#tab/azure-RESTAPI)

Hantera TDE med hjälp av REST API.

Om du vill konfigurera TDE via REST API måste du vara ansluten som Azure-ägare, -deltagare eller SQL-säkerhetshanterare.
Använd följande uppsättning kommandon för Azure SQL Database och Azure Synapse:

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera server](/rest/api/sql/servers/createorupdate)|Lägger till Azure Active Directory identitet till en server. (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera servernyckel](/rest/api/sql/serverkeys/createorupdate)|Lägger till Key Vault nyckel till en server.|
|[Ta bort servernyckel](/rest/api/sql/serverkeys/delete)|Tar bort en Key Vault nyckel från en server. |
|[Hämta servernycklar](/rest/api/sql/serverkeys/get)|Hämtar en Key Vault nyckel från en server.|
|[Visa en lista över servernycklar efter server](/rest/api/sql/serverkeys/listbyserver)|Hämtar Key Vault för en server. |
|[Skapa eller uppdatera krypteringsskydd](/rest/api/sql/encryptionprotectors/createorupdate)|Anger TDE-skydd för en server.|
|[Hämta krypteringsskydd](/rest/api/sql/encryptionprotectors/get)|Hämtar TDE-skyddet för en server.|
|[Lista krypteringsskydd efter server](/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar TDE-skydd för en server. |
|[Skapa eller uppdatera transparent datakryptering konfiguration](/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar TDE för en databas.|
|[Hämta transparent datakryptering konfiguration](/rest/api/sql/transparentdataencryptions/get)|Hämtar TDE-konfigurationen för en databas.|
|[Lista transparent datakryptering konfigurationsresultat](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hämtar krypteringsresultatet för en databas.|

## <a name="see-also"></a>Se även

- SQL Server som körs på en virtuell Azure-dator kan också använda en asymmetrisk nyckel från Key Vault. Konfigurationsstegen skiljer sig från användningen av en asymmetrisk nyckel i SQL Database och SQL Managed Instance. Mer information finns i [Utökningsbar nyckelhantering med hjälp av Azure Key Vault (SQL Server).](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)
- En allmän beskrivning av transparent datakryptering finns i [Transparent datakryptering.](/sql/relational-databases/security/encryption/transparent-data-encryption)
- Mer information om transparent datakryptering med BYOK-stöd för Azure SQL Database, Azure SQL Managed Instance och Azure Synapse finns i [Transparent datakryptering med Bring Your Own Key stöd för](transparent-data-encryption-byok-overview.md).
- Om du vill börja använda transparent datakryptering Bring Your Own Key stöd finns i guiden Aktivera transparent datakryptering med hjälp av din egen [nyckel från Key Vault](transparent-data-encryption-byok-configure.md).
- Mer information om Key Vault finns i [Säker åtkomst till ett nyckelvalv.](../../key-vault/general/security-overview.md)