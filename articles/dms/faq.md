---
title: Vanliga frågor och svar – Azure Database Migration Service
description: Vanliga frågor om hur du använder Azure Database Migration Service för att utföra databasmigreringar.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 83ffd405bf8e39737d2d36c294eac27f9bc76daf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588827"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Vanliga frågor och svar om att använda Azure Database Migration Service

Den här artikeln innehåller vanliga frågor om hur du använder Azure Database Migration Service tillsammans med relaterade svar.

## <a name="overview"></a>Översikt

**F. Vad är Azure Database Migration Service?**
Azure Database Migration Service är en fullständigt hanterad tjänst som har utformats för att möjliggöra sömlös migrering från flera databaskällor till Azure Data-plattformar med minimal avbrottstid. Tjänsten är för närvarande allmänt tillgänglig, med pågående utvecklingsarbete som fokuserar på:

* Tillförlitlighet och prestanda.
* Iterativ addition av käll-målpar.
* Fortsatt investering i friktionsfria migreringar.

**F. Vilka käll-/målpar stöder Azure Database Migration Service för närvarande?**
Tjänsten stöder för närvarande en mängd olika käll-/målpar eller migreringsscenarier. En fullständig lista över status för varje tillgängligt migreringsscenario finns i artikeln Status för migreringsscenarier [som stöds av Azure Database Migration Service](https://github.com/MicrosoftDocs/azure-docs-pr/pull/resource-scenario-status.md).

**F. Vilka versioner SQL Server stöder Azure Database Migration Service som källa?**
När du migrerar från SQL Server kommer källor som stöds för Azure Database Migration Service att SQL Server 2005 till och med SQL Server 2019.

**F: Vad är skillnaden Azure Database Migration Service offline och en onlinemigrering när du använder en onlinemigrering?**
Du kan använda Azure Database Migration Service för att utföra offline- och onlinemigrering. Med  en offlinemigrering startar programmets stilleståndstid när migreringen startar. Med  en onlinemigrering är stilleståndstiden begränsad till tiden för att minska i slutet av migreringen. Vi rekommenderar att du testar en offlinemigrering för att avgöra om det frånkopplade tillståndet är godtagbart. Om det inte är det kan du utföra en onlinemigrering.

> [!NOTE]
> Om Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans som baseras på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

**F. Hur är Azure Database Migration Service jämfört med andra Migreringsverktyg för Microsoft-databaser, till exempel Database Migration Assistant (DMA) eller SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service är den bästa metoden för databasmigrering för Microsoft Azure i stor skala. Mer information om hur Azure Database Migration Service kan jämföras med andra Migreringsverktyg för Microsoft-databaser och rekommendationer om hur du använder tjänsten för olika scenarier finns i blogginlägget [Differentiating Microsoft's Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**F. Hur är Azure Database Migration Service jämfört med Azure Migrate erbjudandet?**
Azure Migrate hjälp med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migreringens lämplighet och prestandabaserade storleksändring och ger kostnadsuppskattningar för att köra dina lokala virtuella datorer i Azure. Azure Migrate är användbart för lift and shift-migreringar av lokala VM-baserade arbetsbelastningar till virtuella Azure IaaS-datorer. Men till skillnad Azure Database Migration Service är Azure Migrate inte ett särskilt erbjudande för databasmigrering för Relationsdatabasplattformar i Azure PaaS, till exempel Azure SQL Database eller Azure SQL Managed Instance.

**F. Lagrar Database Migration Service kunddata?**
Nej. Database Migration Service lagrar inte kunddata.

## <a name="setup"></a>Konfiguration

**F. Vilka är kraven för att använda Azure Database Migration Service?**
Det finns flera förutsättningar som krävs för att säkerställa Azure Database Migration Service körs smidigt när du utför databasmigrering. Några av kraven gäller för alla scenarier (källmålpar) som stöds av tjänsten, medan andra krav är unika för ett specifikt scenario.

Azure Database Migration Service som är gemensamma för alla migreringsscenarier som stöds är bland annat behovet av att:

* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning till dina lokala källservrar med hjälp av [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Kontrollera att reglerna för nätverkssäkerhetsgruppen för det virtuella nätverket inte blockerar port 443 för ServiceTags för ServiceBus, Storage och AzureMonitor. Mer information om filtrering av nätverkssäkerhetsgrupper för virtuella nätverk finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* När du använder en brandväggsinstallation framför dina källdatabaser kan du behöva lägga till brandväggsregler för att tillåta Azure Database Migration Service åtkomst till källdatabaserna för migrering.

En lista över alla förutsättningar som krävs för att konkurrera specifika migreringsscenarier med Azure Database Migration Service finns i relaterade självstudier i Azure Database Migration Service [dokumentationen](./dms-overview.md) om docs.microsoft.com.

**F. Hur gör jag för att hitta IP-adressen för Azure Database Migration Service så att jag kan skapa en lista över tillåtna för brandväggsregler som används för att komma åt min källdatabas för migrering?**
Du kan behöva lägga till brandväggsregler som tillåter Azure Database Migration Service åtkomst till källdatabasen för migrering. IP-adressen för tjänsten är dynamisk, men om du använder ExpressRoute tilldelas den här adressen privat av företagsnätverket. Det enklaste sättet att identifiera lämplig IP-adress är att leta i samma resursgrupp som din etablerade Azure Database Migration Service-resurs för att hitta det associerade nätverksgränssnittet. Vanligtvis börjar namnet på nätverksgränssnittsresursen med NIC-prefixet och följs av ett unikt tecken och en nummersekvens, till exempel NIC-jj6tnztnmarpsskr82rbndyp. Genom att välja den här nätverksgränssnittsresursen kan du se IP-adressen som måste ingå i listan över tillåtna på resursöversikten Azure Portal sidan.

Du kan också behöva inkludera den portkälla som SQL Server lyssnar på listan över tillåtna. Som standard är det port 1433, men SQL Server kan konfigureras för att lyssna på andra portar också. I det här fallet måste du även inkludera dessa portar i listan över tillåtna portar. Du kan fastställa vilken port som SQL Server lyssnar på med hjälp av en fråga i dynamisk hanteringsvy:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Du kan också fastställa vilken port som SQL Server lyssnar genom att fråga SQL Server felloggen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**F. Hur gör jag för att konfigurera en Microsoft Azure Virtual Network?**
Det finns flera Microsoft-självstudier som kan hjälpa dig att konfigurera ett virtuellt nätverk, men den officiella dokumentationen finns i [artikeln Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Användning

**F. Vad är en sammanfattning av de steg som krävs för att använda Azure Database Migration Service för att utföra en databasmigrering?**
Under en typisk enkel databasmigrering gör du följande:

1. Skapa en eller flera måldatabaser.
2. Utvärdera dina källdatabaser.
    * För homogena migreringar utvärderar du dina befintliga databaser med hjälp av [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * För heterogena migreringar (från konkurrerande källor) utvärderar du dina befintliga databaser med [SSMA](/sql/ssma/sql-server-migration-assistant). Du kan också använda SSMA för att konvertera databasobjekt och migrera schemat till målplattformen.
3. Skapa en instans av Azure Database Migration Service.
4. Skapa ett migreringsprojekt som anger källdatabaserna, måldatabaserna och tabellerna som ska migreras.
5. Starta den fullständiga inläsningen.
6. Välj den efterföljande verifieringen.
7. Utför en manuell växling av produktionsmiljön till den nya molnbaserade databasen.

## <a name="troubleshooting-and-optimization"></a>Felsökning och optimering

**F. Jag ställer in ett migreringsprojekt i DMS och jag har problem med att ansluta till min källdatabas. Vad ska jag göra?**
Om du har problem med att ansluta till källdatabassystemet när du arbetar med migreringen kan du skapa en virtuell dator i samma undernät i det virtuella nätverket som du konfigurerade DMS-instansen med. På den virtuella datorn bör du kunna köra ett anslutningstest, till exempel använda en UDL-fil för att testa en anslutning till SQL Server eller ladda ned Robo 3T för att testa MongoDB-anslutningar. Om anslutningstestet lyckas bör du inte ha problem med att ansluta till källdatabasen. Kontakta nätverksadministratören om anslutningstestet inte lyckas.

**F. Varför är min Azure Database Migration Service otillgänglig eller stoppad?**
Om användaren uttryckligen stoppar Azure Database Migration Service (DMS) eller om tjänsten är inaktiv i 24 timmar, är tjänsten i ett stoppat eller automatiskt pausat tillstånd. I varje fall är tjänsten inte tillgänglig och har stoppad status.  Starta om tjänsten för att återuppta aktiva migreringar.

**F. Finns det några rekommendationer för att optimera prestanda för Azure Database Migration Service?**
Du kan göra några saker för att påskynda databasmigrering med hjälp av tjänsten:

* Använd prisnivån multi CPU Generell användning när du skapar tjänstinstansen så att tjänsten kan dra nytta av flera virtuella processorer för parallellisering och snabbare dataöverföring.
* Tillfälligt skala upp din Azure SQL Database-målinstans till SKU på Premium-nivå under datamigreringsåtgärden för att minimera Azure SQL Database-begränsning som kan påverka dataöverföringsaktiviteter när du använder SKU:er på lägre nivå.

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Azure Database Migration Service](dms-overview.md).
