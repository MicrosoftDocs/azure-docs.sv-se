---
title: Transparent datakryptering
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: En översikt över transparent data kryptering för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Dokumentet täcker fördelarna och alternativen för konfiguration, som innehåller tjänst hanterad transparent data kryptering och Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791400"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent data kryptering för SQL Database, SQL-hanterad instans och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) hjälper till att skydda Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse-analys mot hot från skadlig offline-aktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade SQL-databaser och måste aktive ras manuellt för äldre databaser av Azure SQL Database, Azure SQL-hanterad instans. TDE måste aktive ras manuellt för Azure Synapse Analytics.

TDE utför I/O-kryptering i real tid och dekryptering av data på sidnivå. Varje sida dekrypteras när de läses in i minnet och krypteras sedan innan de skrivs tillbaka till disken. TDE krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel (DEK). Vid databas start dekrypteras den krypterade DEK och används sedan för dekryptering och Omkryptering av databasfilerna i processen för SQL Server databas motor. DEK skyddas av TDE-skyddet. TDE-skydd är antingen ett tjänstehanterat certifikat (hanterad transparent data kryptering) eller en asymmetrisk nyckel som lagras i [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) (kundhanterad transparent data kryptering).

För Azure SQL Database och Azure-Synapse anges TDE-skyddet på [Server](logical-servers.md) nivå och ärvs av alla databaser som är kopplade till den servern. För Azure SQL-hanterad instans anges TDE-skydd på instans nivå och den ärvs av alla krypterade databaser på den instansen. Termen *Server* avser både server och instans i det här dokumentet, om inget annat anges.

> [!IMPORTANT]
> Alla nyligen skapade databaser i SQL Database krypteras som standard med hjälp av tjänst hanterad transparent data kryptering. Befintliga SQL-databaser som skapats före maj 2017 och SQL-databaser som skapats via återställning, geo-replikering och databas kopiering är inte krypterade som standard. Befintliga SQL-hanterade instans databaser som skapats före februari 2019 krypteras inte som standard. SQL-hanterade instans databaser som skapats via återställning ärver krypterings status från källan.

> [!NOTE]
> TDE kan inte användas för att kryptera system databaser, till exempel **huvud** databasen, i Azure SQL Database och Azure SQL-hanterad instans. **Huvud** databasen innehåller objekt som behövs för att utföra TDE-åtgärder på användar databaserna. Vi rekommenderar att inte lagra känsliga data i system databaserna. [Infrastruktur krypteringen](transparent-data-encryption-byok-overview.md#doubleencryption) är nu distribuerad och krypterar system databaser inklusive Master. 

## <a name="service-managed-transparent-data-encryption"></a>Hanterad transparent data kryptering i tjänst

I Azure är standardinställningen för TDE att DEK skyddas av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje server och krypteringsalgoritmen som används är AES 256. Om en databas finns i en geo-replikeringsrelation, skyddas både de primära och geo-sekundära databaserna av den primära databasens överordnade server nyckel. Om två databaser är anslutna till samma server, delar de också samma inbyggda certifikat. Microsoft roterar dessa certifikat automatiskt i enlighet med den interna säkerhets principen och rot nyckeln skyddas av en intern Microsoft-lagringsplats. Kunder kan verifiera att SQL Database-och SQL-hanterad instans uppfyller interna säkerhets principer i oberoende gransknings rapporter från tredje part som är tillgängliga i [Microsoft Trust Center](https://servicetrust.microsoft.com/).

Microsoft flyttar också sömlöst och hanterar nycklarna efter behov för geo-replikering och återställning.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Kundhanterad transparent data kryptering – Bring Your Own Key

Kundhanterade TDE kallas även Bring Your Own Key (BYOK)-stöd för TDE. I det här scenariot är TDE-skyddskomponenten som krypterar DEK en kundhanterad asymmetrisk nyckel, som lagras i ett kundägda och hanterat Azure Key Vault (Azures molnbaserade externt nyckel hanterings system) och lämnar aldrig nyckel valvet. TDE-skydd kan [genereras av nyckel valvet eller överföras till nyckel valvet](../../key-vault/keys/hsm-protected-keys.md) från en lokal HSM-enhet (Hardware Security Module). SQL Database, SQL-hanterad instans och Azure-Synapse måste beviljas behörighet till det kundägda nyckel valvet för att dekryptera och kryptera DEK. Om serverns behörigheter för nyckel valvet har återkallats går det inte att komma åt databasen och alla data krypteras

Med TDE med Azure Key Vault-integrering kan användare styra viktiga hanterings uppgifter, till exempel nyckel rotationer, nyckel valv, nyckel säkerhets kopior och aktivera granskning/rapportering på alla TDE-skydd med hjälp av Azure Key Vault-funktioner. Key Vault tillhandahåller central nyckel hantering, utnyttjar nära övervakade HSM: er och möjliggör separering av uppgifter mellan hantering av nycklar och data för att möta efterlevnaden av säkerhets principer.
Mer information om BYOK för Azure SQL Database och Azure-Synapse finns i [transparent data kryptering med Azure Key Vault-integration](transparent-data-encryption-byok-overview.md).

Om du vill börja använda TDE med Azure Key Vault-integrering kan du läsa mer i instruktions guiden [Aktivera transparent data kryptering genom att använda din egen nyckel från Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent data krypterings-skyddad databas

Du behöver inte dekryptera databaser för åtgärder i Azure. TDE-inställningarna för käll databasen eller den primära databasen ärvs transparent på målet. Åtgärder som ingår:

- Geo-återställning
- Självbetjäning för återställning av tidpunkt
- Återställning av en borttagen databas
- Aktiv geo-replikering
- Skapandet av en databas kopia
- Återställning av säkerhets kopierings filen till Azure SQL-hanterad instans

> [!IMPORTANT]
> Att göra en manuell säkerhets kopiering av en databas som har krypterats av en hanterad TDE stöds inte i Azure SQL-hanterad instans eftersom certifikatet som används för kryptering inte är tillgängligt. Använd funktionen för återställning av tidpunkt för att flytta den här typen av databas till en annan SQL-hanterad instans eller växla till kundhanterad nyckel.

När du exporterar en TDE databas krypteras inte det exporterade innehållet i databasen. Det exporterade innehållet lagras i okrypterade BACPAC-filer. Se till att skydda BACPAC-filerna på rätt sätt och aktivera TDE efter att den nya databasen har importer ATS.

Om filen BACPAC till exempel exporteras från en SQL Server instans, krypteras inte det importerade innehållet i den nya databasen automatiskt. På samma sätt, om BACPAC-filen importeras till en SQL Server instans, krypteras inte den nya databasen automatiskt.

Det enda undantaget är när du exporterar en databas till och från SQL Database. TDE har Aktiver ATS för den nya databasen, men BACPAC-filen är fortfarande inte krypterad.

## <a name="manage-transparent-data-encryption"></a>Hantera transparent data kryptering

# <a name="the-azure-portal"></a>[Azure-portalen](#tab/azure-portal)

Hantera TDE i Azure Portal.

Om du vill konfigurera TDE via Azure Portal måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

Aktivera och inaktivera TDE på databas nivå. För Azure SQL-hanterad instans använder du Transact-SQL (T-SQL) för att aktivera och inaktivera TDE på en databas. För Azure SQL Database och Azure-Synapse kan du hantera TDE för databasen i [Azure Portal](https://portal.azure.com) när du har loggat in med Azure-administratören eller deltagar kontot. Hitta TDE-inställningarna under din användar databas. Som standard används hanterad transparent data kryptering. Ett TDE-certifikat skapas automatiskt för den server som innehåller-databasen.

![Hanterad transparent data kryptering i tjänst](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Du ställer in huvud nyckeln TDE, som kallas TDE-skydd, på Server-eller instans nivå. Om du vill använda TDE med BYOK-stöd och skydda dina databaser med en nyckel från Key Vault öppnar du TDE-inställningarna under din server.

![Transparent data kryptering med stöd för Bring Your Own Key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hantera TDE med hjälp av PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill konfigurera TDE via PowerShell måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets för Azure SQL Database och Azure-Synapse

Använd följande cmdlets för Azure SQL Database och Azure-Synapse:

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent data kryptering för en databas.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hämtar transparent data krypterings tillstånd för en databas. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontrollerar krypterings förloppet för en databas. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Lägger till en Key Vault nyckel till en server. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hämtar Key Vault nycklar för en server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ställer in transparent data krypterings skydd för en server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hämtar skyddet för transparent data kryptering |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Tar bort en Key Vault nyckel från en server. |
|  | |

> [!IMPORTANT]
> För Azure SQL-hanterad instans använder du T-SQL [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) -kommandot för att aktivera och inaktivera TDE på en databas nivå och kontrol lera [PowerShell-skriptet](transparent-data-encryption-byok-configure.md) för att hantera TDE på en instans nivå.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Hantera TDE med hjälp av Transact-SQL.

Anslut till databasen med en inloggning som är en administratör eller medlem av **DBManager** -rollen i huvud databasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | Ange kryptering vid/från krypterar eller dekrypterar en databas |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypterings tillstånd för en databas och dess tillhör ande databas krypterings nycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypterings status för varje Azure Synapse-nod och dess tillhör ande databas krypterings nycklar |
|  | |

Du kan inte byta TDE-skydd till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure Portal.

# <a name="rest-api"></a>[REST-API](#tab/azure-RESTAPI)

Hantera TDE med hjälp av REST API.

Om du vill konfigurera TDE via REST API måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.
Använd följande uppsättning kommandon för Azure SQL Database och Azure-Synapse:

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera Server](/rest/api/sql/servers/createorupdate)|Lägger till en Azure Active Directory identitet till en server. (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera server nyckel](/rest/api/sql/serverkeys/createorupdate)|Lägger till en Key Vault nyckel till en server.|
|[Ta bort server nyckel](/rest/api/sql/serverkeys/delete)|Tar bort en Key Vault nyckel från en server. |
|[Hämta Server nycklar](/rest/api/sql/serverkeys/get)|Hämtar en speciell Key Vault nyckel från en server.|
|[Visa en lista med Server nycklar per server](/rest/api/sql/serverkeys/listbyserver)|Hämtar Key Vault nycklar för en server. |
|[Skapa eller uppdatera krypterings skydd](/rest/api/sql/encryptionprotectors/createorupdate)|Anger TDE-skydd för en server.|
|[Hämta krypterings skydd](/rest/api/sql/encryptionprotectors/get)|Hämtar TDE-skydd för en server.|
|[Visa lista över krypterings skydd per server](/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar TDE-skydd för en server. |
|[Skapa eller uppdatera transparent datakryptering konfiguration](/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar TDE för en databas.|
|[Hämta transparent datakryptering konfiguration](/rest/api/sql/transparentdataencryptions/get)|Hämtar TDE-konfigurationen för en databas.|
|[Visa lista transparent datakryptering konfigurations resultat](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hämtar krypterings resultatet för en databas.|

## <a name="see-also"></a>Se även

- SQL Server som körs på en virtuell Azure-dator kan också använda en asymmetrisk nyckel från Key Vault. Konfigurations stegen skiljer sig från att använda en asymmetrisk nyckel i SQL Database-och SQL-hanterad instans. Mer information finns i [utöknings bar nyckel hantering med hjälp av Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- En allmän beskrivning av TDE finns i [transparent data kryptering](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Om du vill veta mer om TDE med BYOK-stöd för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse, se [transparent data kryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-overview.md).
- Om du vill börja använda TDE med Bring Your Own Key support kan du läsa instruktions guiden och [Aktivera transparent data kryptering med hjälp av din egen nyckel från Key Vault](transparent-data-encryption-byok-configure.md).
- Mer information om Key Vault finns i [säker åtkomst till ett nyckel valv](../../key-vault/general/secure-your-key-vault.md).