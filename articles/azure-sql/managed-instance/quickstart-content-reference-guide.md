---
title: Innehållsreferens för att komma igång
titleSuffix: Azure SQL Managed Instance
description: 'En referens för innehåll som hjälper dig att komma igång med Azure SQL Managed Instance. '
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
ms.openlocfilehash: ec767e5f3a88c52f9686eec8f7e458ab517ee35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784343"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Komma igång med Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) skapar en databas med nära 100 % kompatibilitet med den senaste SQL Server-databasmotorn (Enterprise Edition), vilket ger en intern implementering av virtuella [](https://azure.microsoft.com/pricing/details/sql-database/) nätverk [(VNet)](../../virtual-network/virtual-networks-overview.md) som åtgärdar vanliga säkerhetsproblem och en affärsmodell som är fördelaktig för befintliga SQL Server-kunder.

I den här artikeln hittar du referenser till innehåll som lär dig hur du snabbt konfigurerar och skapar en SQL Managed Instance och migrerar dina databaser.

## <a name="quickstart-overview"></a>Översikt över Snabbstart

Med följande snabbstarter kan du snabbt skapa en SQL Managed Instance, konfigurera en virtuell dator eller punkt-till-plats-VPN-anslutning för klientprogram och återställa en databas till din nya SQL Managed Instance med hjälp av en `.bak` fil.

### <a name="configure-environment"></a>Konfigurera miljön

Som ett första steg måste du skapa din första SQL Managed Instance med nätverksmiljön där den kommer att placeras och aktivera anslutning från den dator eller virtuella dator där du kör frågor för att SQL Managed Instance. Du kan använda följande guider:

- [Skapa en SQL Managed Instance med hjälp av Azure Portal](instance-create-quickstart.md). I Azure-portalen konfigurerar du nödvändiga parametrar (användarnamn/lösenord, antal kärnor och maximal lagringsmängd) och skapar automatiskt Azure-nätverksmiljön utan att behöva känna till nätverksinformation och infrastrukturskrav. Du behöver bara se till att du har [en prenumerationstyp](resource-limits.md#supported-subscription-types) som för närvarande kan skapa en SQL Managed Instance. Om du har ett eget nätverk som du vill använda eller om du vill anpassa nätverket kan du gå till Konfigurera ett befintligt [virtuellt nätverk för Azure SQL Managed Instance](vnet-existing-add-subnet.md) eller skapa ett [virtuellt](virtual-network-subnet-create-arm-template.md)nätverk för Azure SQL Managed Instance .
- En SQL Managed Instance skapas i ett eget VNet utan offentlig slutpunkt. För klientprogramåtkomst kan du skapa antingen **en virtuell dator i samma virtuella nätverk (olika undernät)** eller **en punkt-till-plats-VPN-anslutning till det virtuella nätverket från klientdatorn** med hjälp av någon av dessa snabbstarter:
  - Aktivera [offentlig slutpunkt](public-endpoint-configure.md) på din SQL Managed Instance för att komma åt dina data direkt från din miljö.
  - Skapa [en virtuell Azure-dator i SQL Managed Instance virtuella nätverk för](connect-vm-instance-configure.md) klientprogramanslutning, inklusive SQL Server Management Studio.
  - Konfigurera [punkt-till-plats-VPN-anslutning till din SQL Managed Instance](point-to-site-p2s-configure.md) från den klientdator där du har SQL Server Management Studio och andra klientanslutningsprogram. Det här är ett annat av två alternativ för anslutning till SQL Managed Instance och till dess virtuella nätverk.

  > [!NOTE]
  > - Du kan även använda ExpressRoute- eller plats-till-plats-anslutning från ditt lokala nätverk, men dessa metoder ligger utanför det område som behandlas i de här snabbstarterna.
  > - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, bör du observera att kvarhållningen endast gäller för loggar som skrivits efter att kvarhållningsvärdet har ändrats (loggar som skrivits under perioden när kvarhållningen var inställt på obegränsad bevaras, även efter att kvarhållning har aktiverats).

Som ett alternativ till att skapa SQL Managed Instance manuellt kan du använda [PowerShell,](scripts/create-configure-managed-instance-powershell.md) [PowerShell med Resource Manager mall](./create-template-quickstart.md)eller [Azure CLI](/cli/azure/sql/mi#az_sql_mi_create) för att skripta och automatisera den här processen.

### <a name="migrate-your-databases"></a>Migrera dina databaser

När du har skapat SQL Managed Instance och konfigurerat åtkomst kan du börja migrera dina SQL Server databaser. Migreringen kan misslyckas om du har vissa funktioner som inte stöds i källdatabasen som du vill migrera. För att undvika fel och kontrollera kompatibilitet kan du använda [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) för att analysera dina databaser på SQL Server och hitta eventuella problem som kan blockera migrering till en SQL Managed Instance, till exempel förekomsten av [FileStream](/sql/relational-databases/blob/filestream-sql-server) eller flera loggfiler. Om du löser dessa problem är dina databaser redo att migreras till SQL Managed Instance. [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) är ett annat användbart verktyg som kan registrera din arbetsbelastning på SQL Server och spela upp den igen på en SQL Managed Instance så att du kan avgöra om det kommer att finnas några prestandaproblem om du migrerar till en SQL Managed Instance.

När du är säker på att du kan migrera databasen till en SQL Managed Instance kan du använda de inbyggda funktionerna för SQL Server-återställning för att återställa en databas till en SQL Managed Instance från en `.bak` fil. Du kan använda den här metoden för att migrera databaser SQL Server en databasmotor som är installerad lokalt eller i Azure Virtual Machines. En snabbstart finns i Återställa [från säkerhetskopia till en SQL Managed Instance](restore-sample-database-quickstart.md). I den här snabbstarten återställer du från en `.bak`-fil som lagras i Azures bloblagring med hjälp av Transact-SQL-kommandot `RESTORE`.

> [!TIP]
> Mer information om användning av Transact-SQL-kommandot `BACKUP` för att skapa en säkerhetskopia av din databas i Azures bloblagring finns i [SQL Server-säkerhetskopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Med de här snabbstarterna kan du snabbt skapa, konfigurera och återställa säkerhetskopia av databasen till en SQL Managed Instance. I vissa fall skulle du behöva anpassa eller automatisera distributionen av SQL Managed Instance och den nätverksmiljö som krävs. De här scenarierna beskrivs nedan.

## <a name="customize-network-environment"></a>Anpassa nätverksmiljön

Även om det virtuella nätverket/undernätet kan konfigureras automatiskt när instansen skapas med hjälp av Azure Portal kan det vara bra att skapa det innan du börjar skapa instanser i [SQL Managed Instance](instance-create-quickstart.md)eftersom du kan konfigurera parametrarna för VNet och undernät. Det enklaste sättet att skapa och konfigurera nätverksmiljön är att använda distributionsmallen för [Azure-resurser](virtual-network-subnet-create-arm-template.md) som skapar och konfigurerar nätverket och undernätet där instansen ska placeras. Du behöver bara trycka på knappen för Azure Resource Manager-distribution och fylla i formuläret med parametrar.

Alternativt kan du använda det här [PowerShell-skriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) för att automatisera skapandet av nätverket.

Om du redan har ett VNet och undernät där du vill distribuera din SQL Managed Instance, måste du se till att ditt VNet och undernät uppfyller [nätverkskraven](connectivity-architecture-overview.md#network-requirements). Använd det här [PowerShell-skriptet för att kontrollera att undernätet är korrekt konfigurerat](vnet-existing-add-subnet.md). Det här skriptet verifierar nätverket och rapporterar eventuella problem som talar om för dig vad som ska ändras och erbjuder sedan att göra nödvändiga ändringar i ditt VNet/undernät. Kör det här skriptet om du inte vill konfigurera ditt virtuella nätverk/undernät manuellt. Du kan även köra det efter större omkonfigurationer av nätverksinfrastrukturen. Om du vill skapa och konfigurera [](connectivity-architecture-overview.md) ett eget nätverk läser du anslutningsarkitekturen och den här ultimata guiden för att skapa och [konfigurera en SQL Managed Instance miljö.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-sql-managed-instance"></a>Migrera till en SQL Managed Instance

Med de tidigare nämnda snabbstarterna kan du snabbt konfigurera en SQL Managed Instance och flytta dina databaser med den `RESTORE` inbyggda funktionen. Det här är en bra utgångspunkt om du vill slutföra ett snabbt konceptbevis och kontrollera att din lösning kan fungera med hanterad instans.

Men för att migrera produktionsdatabasen eller till och med dev/test-databaser som du vill använda för vissa prestandatest, måste du överväga att använda några ytterligare tekniker, till exempel:

- Prestandatestning – Du bör mäta baslinjeprestandamått på din SQL Server-källinstans och jämföra dem med prestandamåtten på den SQL Managed Instance där du har migrerat databasen. Läs mer om [metodtipsen för prestandajämförelse.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Onlinemigrering – Med den inbyggda beskrivningen i den här artikeln måste du vänta tills databaserna har återställts (och kopierats till Azure Blob Storage om de inte `RESTORE` redan lagras där). Detta leder till viss avbrottstid för ditt program, särskilt för större databaser. Om du vill flytta produktionsdatabasen använder du [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) för att migrera din databas med minimal avbrottstid. DMS åstadkommer detta genom att inkrementellt push-pusha ändringarna som gjorts i källdatabasen till SQL Managed Instance databasen som återställs. På så sätt kan du snabbt växla programmet från källdatabasen till måldatabasen med minimal avbrottstid.

Läs mer om den [rekommenderade migreringsprocessen](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Nästa steg

- Hitta en [lista på hög nivå över funktioner som stöds i SQL Managed Instance här](../database/features-comparison.md) och information och kända problem [här.](transact-sql-tsql-differences-sql-server.md)
- Lär dig [mer om tekniska egenskaper för SQL Managed Instance](resource-limits.md#service-tier-characteristics).
- Mer avancerade how-to-s finns i [hur du använder en SQL Managed Instance](how-to-content-reference-guide.md).
- [Identifiera rätt Azure SQL Managed Instance SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).