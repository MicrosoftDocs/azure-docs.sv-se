---
title: Innehålls referens för att komma igång
titleSuffix: Azure SQL Managed Instance
description: 'En referens för innehåll som hjälper dig att komma igång med Azure SQL-hanterad instans. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690975"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Komma igång med Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md) skapar en databas med nära 100%-kompatibilitet med den senaste SQL Server (Enterprise Edition) databas motorn, som tillhandahåller en inbyggd [virtuell nätverks implementering (VNet)](../../virtual-network/virtual-networks-overview.md) som behandlar vanliga säkerhets problem och en [affärs modell](https://azure.microsoft.com/pricing/details/sql-database/) som är fördelaktig för befintliga SQL Server kunder.

I den här artikeln hittar du referenser till innehåll som visar hur du snabbt konfigurerar och skapar en SQL-hanterad instans och migrerar dina databaser.

## <a name="quickstart-overview"></a>Översikt över Snabbstart

Med följande snabb Starter kan du snabbt skapa en SQL-hanterad instans, konfigurera en virtuell dator eller peka på plats-VPN-anslutning för klient program och återställa en databas till din nya SQL-hanterade instans med hjälp av en `.bak` fil.

### <a name="configure-environment"></a>Konfigurera miljön

Som ett första steg måste du skapa din första SQL-hanterade instans med nätverks miljön där den kommer att placeras och aktivera anslutning från datorn eller den virtuella datorn där du kör frågor till SQL-hanterad instans. Du kan använda följande guider:

- [Skapa en SQL-hanterad instans med hjälp av Azure Portal](instance-create-quickstart.md). I Azure-portalen konfigurerar du nödvändiga parametrar (användarnamn/lösenord, antal kärnor och maximal lagringsmängd) och skapar automatiskt Azure-nätverksmiljön utan att behöva känna till nätverksinformation och infrastrukturskrav. Du ser bara till att du har en [prenumerations typ](resource-limits.md#supported-subscription-types) som för närvarande tillåts att skapa en SQL-hanterad instans. Om du har ett eget nätverk som du vill använda eller vill anpassa nätverket, se [Konfigurera ett befintligt virtuellt nätverk för Azure SQL-hanterad instans](vnet-existing-add-subnet.md) eller [skapa ett virtuellt nätverk för Azure SQL-hanterad instans](virtual-network-subnet-create-arm-template.md).
- En SQL-hanterad instans skapas i sitt eget VNet utan en offentlig slut punkt. För klientprogramåtkomst kan du skapa antingen **en virtuell dator i samma virtuella nätverk (olika undernät)** eller **en punkt-till-plats-VPN-anslutning till det virtuella nätverket från klientdatorn** med hjälp av någon av dessa snabbstarter:
  - Aktivera [offentlig slut punkt](public-endpoint-configure.md) på din SQL-hanterade instans för att komma åt dina data direkt från din miljö.
  - Skapa [en virtuell Azure-dator i VNet för SQL-hanterad instans](connect-vm-instance-configure.md) för klient program anslutning, inklusive SQL Server Management Studio.
  - Konfigurera [punkt-till-plats-VPN-anslutning till din SQL-hanterade instans](point-to-site-p2s-configure.md) från klient datorn där du har SQL Server Management Studio och andra klient anslutnings program. Detta är andra av två alternativ för anslutning till din SQL-hanterade instans och dess VNet.

  > [!NOTE]
  > - Du kan även använda ExpressRoute- eller plats-till-plats-anslutning från ditt lokala nätverk, men dessa metoder ligger utanför det område som behandlas i de här snabbstarterna.
  > - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, Observera att kvarhållning endast kommer att gälla för loggar som skrivits efter att kvarhållning har ändrats (loggar som skrivits under perioden när kvarhållning hade värdet obegränsat bevaras, även efter att kvarhållning har Aktiver ATS).

Som ett alternativ till manuell generering av SQL-hanterad instans kan du använda [PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell med Resource Manager-mall](./create-template-quickstart.md)eller [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) för att skapa skript och automatisera processen.

### <a name="migrate-your-databases"></a>Migrera dina databaser

När du har skapat en SQL-hanterad instans och konfigurerat åtkomst kan du börja migrera dina SQL Server-databaser. Migreringen kan gå sönder om du har funktioner som inte stöds i käll databasen som du vill migrera. För att undvika fel och kontrol lera kompatibiliteten kan du använda [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) för att analysera dina databaser på SQL Server och hitta eventuella problem som kan blockera migrering till en SQL-hanterad instans, till exempel förekomst av [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) eller flera loggfiler. Om du löser problemen är dina databaser klara att migreras till SQL-hanterad instans. [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) är ett annat användbart verktyg som kan registrera din arbets belastning på SQL Server och spela upp det på en SQL-hanterad instans så att du kan avgöra om du migrerar till en SQL-hanterad instans.

När du är säker på att du kan migrera databasen till en SQL-hanterad instans kan du använda de inbyggda SQL Server återställnings funktionerna för att återställa en databas till en SQL-hanterad instans från en `.bak` fil. Du kan använda den här metoden för att migrera databaser från SQL Server databas motor som är installerade lokalt eller Azure Virtual Machines. En snabb start finns i [återställa från en säkerhets kopia till en SQL-hanterad instans](restore-sample-database-quickstart.md). I den här snabbstarten återställer du från en `.bak`-fil som lagras i Azures bloblagring med hjälp av Transact-SQL-kommandot `RESTORE`.

> [!TIP]
> Mer information om användning av Transact-SQL-kommandot `BACKUP` för att skapa en säkerhetskopia av din databas i Azures bloblagring finns i [SQL Server-säkerhetskopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Med de här snabb starterna kan du snabbt skapa, konfigurera och återställa en säkerhets kopia av databasen till en SQL-hanterad instans. I vissa fall behöver du anpassa eller automatisera distributionen av SQL-hanterad instans och nödvändig nätverks miljö. De här scenarierna beskrivs nedan.

## <a name="customize-network-environment"></a>Anpassa nätverksmiljön

Även om VNet/undernät kan konfigureras automatiskt när instansen [skapas med hjälp av Azure Portal](instance-create-quickstart.md), kan det vara klokt att skapa det innan du börjar skapa instanser i SQL-hanterad instans eftersom du kan konfigurera parametrarna för VNet och undernät. Det enklaste sättet att skapa och konfigurera nätverks miljön är att använda mallen för [distribution av Azure-resurser](virtual-network-subnet-create-arm-template.md) som skapar och konfigurerar ditt nätverk och undernät där instansen kommer att placeras. Du behöver bara trycka på knappen för Azure Resource Manager-distribution och fylla i formuläret med parametrar.

Alternativt kan du använda det här [PowerShell-skriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) för att automatisera skapandet av nätverket.

Om du redan har ett VNet och undernät där du vill distribuera din SQL-hanterade instans måste du kontrol lera att ditt VNet och undernät uppfyller [nätverks kraven](connectivity-architecture-overview.md#network-requirements). Använd det här [PowerShell-skriptet för att kontrollera att undernätet är korrekt konfigurerat](vnet-existing-add-subnet.md). Det här skriptet validerar nätverket och rapporterar eventuella problem, vilket anger vad som ska ändras och sedan erbjudanden för att göra nödvändiga ändringar i ditt VNet/undernät. Kör det här skriptet om du inte vill konfigurera ditt virtuella nätverk/undernät manuellt. Du kan även köra det efter större omkonfigurationer av nätverksinfrastrukturen. Om du vill skapa och konfigurera ditt eget nätverk kan du läsa [anslutnings arkitekturen](connectivity-architecture-overview.md) och den här [ultimata guiden för att skapa och konfigurera en SQL-hanterad instans miljö](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrera till en SQL-hanterad instans

Tidigare angivna snabb starter gör att du snabbt kan skapa en SQL-hanterad instans och flytta dina databaser med hjälp av den inbyggda `RESTORE` funktionen. Det här är en bra utgångs punkt om du vill slutföra snabba korrektur koncept och kontrol lera att din lösning kan arbeta på en hanterad instans.

För att du ska kunna migrera produktions databasen eller till och med utvecklings-och test databaser som du vill använda för en viss prestandatest måste du dock överväga att använda vissa ytterligare metoder, till exempel:

- Prestandatest – du bör mäta bas linje prestanda måtten på din käll SQL Servers instans och jämföra dem med prestanda måtten på den mål SQL-hanterade instans där du har migrerat databasen. Läs mer om [bästa praxis för jämförelse av prestanda](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online-migrering – med den inbyggda `RESTORE` beskrivningen i den här artikeln måste du vänta på att databaserna ska återställas (och kopieras till Azure Blob Storage om de inte redan finns där). Detta leder till viss avbrottstid för ditt program, särskilt för större databaser. Om du vill flytta produktionsdatabasen använder du [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) för att migrera din databas med minimal avbrottstid. DMS utför detta genom att stegvis skicka de ändringar som gjorts i käll databasen till den SQL-hanterade instans databas som återställs. På så sätt kan du snabbt växla ditt program från källa till mål databas med minimal stillestånds tid.

Läs mer om den [rekommenderade migreringsprocessen](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Nästa steg

- Hitta en [övergripande lista med funktioner som stöds i SQL-hanterad instans här](../database/features-comparison.md) och [information och kända problem här](transact-sql-tsql-differences-sql-server.md).
- Lär dig mer om [tekniska egenskaper för SQL-hanterad instans](resource-limits.md#service-tier-characteristics).
- Hitta mer avancerade anvisningar för [hur du använder en SQL-hanterad instans](how-to-content-reference-guide.md).
- [Identifiera rätt SKU för Azure SQL-hanterad instans för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).