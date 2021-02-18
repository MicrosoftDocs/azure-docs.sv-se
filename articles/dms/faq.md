---
title: Vanliga frågor och svar – Azure Database Migration Service
description: Vanliga frågor om hur du använder Azure Database Migration Service för att utföra migrering av databaser.
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
ms.openlocfilehash: f4d65c97bfccd223453583b25ee0586c5bc0b1ec
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091446"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Vanliga frågor om att använda Azure Database Migration Service

Den här artikeln innehåller vanliga frågor om hur du använder Azure Database Migration Service tillsammans med relaterade svar.

## <a name="overview"></a>Översikt

**C. Vad är Azure Database Migration Service?**
Azure Database Migration Service är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid. Tjänsten är för närvarande allmänt tillgänglig, med pågående utvecklings insatser som fokuserar på:

* Tillförlitlighet och prestanda.
* Upprepat tillägg av käll mål par.
* Fortsatt investering i friktion – kostnads fria migreringar.

**C. Vilka käll-/mål-par har Azure Database Migration Service för närvarande stöd för?**
Tjänsten har stöd för en rad olika käll-/mål par, eller migrerings scenarier. En fullständig lista över statusen för varje tillgängligt migrerings scenario finns i artikeln [status för de migrerings scenarier som stöds av Azure Database migration service](./resource-scenario-status.md).

Andra scenarier för migrering finns i för hands version och kräver att du skickar en utnämning via DMS Preview-webbplatsen. En fullständig lista över scenarierna i för hands versionen och för att registrera dig för att delta i något av dessa erbjudanden finns i [DMS Preview-webbplatsen](https://aka.ms/dms-preview/).

**C. Vilka versioner av SQL Server har Azure Database Migration Service stöd för som en källa?**
När du migrerar från SQL Server är källor som stöds för Azure Database Migration Service SQL Server 2005 till och med SQL Server 2019.

**F: Vad är skillnaden mellan en offline-och en online-migrering när du använder Azure Database Migration Service?**
Du kan använda Azure Database Migration Service för att utföra offline-och online-migreringar. Med en *offline* -migrering startar Application nedtid när migreringen startar. Vid en *online* -migrering är stillestånds tiden begränsad till den tid det tar att skära i slutet av migreringen. Vi rekommenderar att du testar en offlinemigrering för att avgöra om det frånkopplade tillståndet är godtagbart. Om det inte är det kan du utföra en onlinemigrering.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

**C. Hur jämförs Azure Database Migration Service med andra Migreringsverktyg för Microsoft Database, till exempel databas Migration Assistant (DMA) eller SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service är den bästa metoden för migrering av databasen till Microsoft Azure i stor skala. Mer information om hur Azure Database Migration Service jämför med andra Migreringsverktyg för Microsoft Database och rekommendationer om hur du använder tjänsten för olika scenarier finns i blogg inlägget [differentiering Microsofts verktyg och tjänster för databas migrering](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**C. Hur jämför Azure Database Migration Service med det Azure Migrate erbjudandet?**
Azure Migrate hjälper till med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migreringens lämplighet och prestanda beroende storlek och ger kostnads uppskattningar för att köra dina lokala virtuella datorer i Azure. Azure Migrate är användbart för migrering av lokala VM-baserade arbets belastningar till virtuella Azure IaaS-datorer. Men till skillnad från Azure Database Migration Service är Azure Migrate inte en specialiserad databas migrations tjänst för Azure PaaS Relations databas plattformar som Azure SQL Database eller Azure SQL-hanterad instans.

**C. Lagrar Database Migration Service kund information?**
Nej. Database Migration Service lagrar inte kund information.

## <a name="setup"></a>Installation

**C. Vilka är kraven för att använda Azure Database Migration Service?**
Det krävs flera krav för att säkerställa att Azure Database Migration Service fungerar smidigt när du utför databas migreringar. Vissa krav gäller för alla scenarier (käll mål par) som stöds av tjänsten, medan andra krav är unika för ett visst scenario.

Azure Database Migration Service förutsättningar som är gemensamma för alla typer av migrering som stöds omfattar behovet av att:

* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Se till att de virtuella nätverkets säkerhets grupp regler inte blockerar port 443 för ServiceTags av Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* När du använder en brand Väggs installation framför dina käll databaser, kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering.

En lista över alla krav som krävs för att konkurrera särskilda migrerings scenarier med hjälp av Azure Database Migration Service finns i de relaterade självstudierna i Azure Database Migration Service- [dokumentationen](./dms-overview.md) på docs.Microsoft.com.

**C. Hur gör jag för att hitta IP-adressen för Azure Database Migration Service så att jag kan skapa en lista över tillåtna brand Väggs regler som används för att få åtkomst till min käll databas för migrering?**
Du kan behöva lägga till brand Väggs regler som ger Azure Database Migration Service åtkomst till din käll databas för migrering. IP-adressen för tjänsten är dynamisk, men om du använder ExpressRoute är adressen privat tilldelad till företags nätverket. Det enklaste sättet att identifiera rätt IP-adress är att titta i samma resurs grupp som din etablerade Azure Database Migration Service resurs för att hitta det associerade nätverks gränssnittet. Normalt börjar namnet på nätverks gränssnitts resursen med NIC-prefixet och följt av ett unikt Character och en nummersekvens, till exempel NIC-jj6tnztnmarpsskr82rbndyp. Genom att välja den här nätverks gränssnitts resursen kan du se IP-adressen som måste tas med i listan över tillåtna på sidan resurs översikt Azure Portal.

Du kan också behöva inkludera port källan som SQL Server lyssnar på listan över tillåtna. Som standard är det port 1433, men käll SQL Server kan konfigureras för att lyssna på andra portar också. I så fall måste du även inkludera dessa portar i listan över tillåtna. Du kan bestämma vilken port som SQL Server lyssnar på genom att använda en dynamisk hanterings fråga:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Du kan också bestämma vilken port som SQL Server lyssnar genom att skicka frågor till SQL Server fel loggen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**C. Hur gör jag för att skapa en Microsoft Azure Virtual Network?**
Även om flera Microsoft-självstudier som kan hjälpa dig genom processen med att konfigurera ett virtuellt nätverk visas den officiella dokumentationen i artikeln [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Användning

**C. Vad är en sammanfattning av de steg som krävs för att använda Azure Database Migration Service för att utföra en migrering av databasen?**
Under en vanlig, enkel databas-migrering, gör du följande:

1. Skapa en eller flera mål databaser.
2. Utvärdera dina käll databaser.
    * För homogena migreringar kan du utvärdera din eller dina befintliga databaser med hjälp av [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * För heterogena migreringar (från konkurrens kraftiga källor) kan du utvärdera dina befintliga databaser med [SSMA](/sql/ssma/sql-server-migration-assistant). Du kan också använda SSMA för att konvertera databas objekt och migrera schemat till din mål plattform.
3. Skapa en instans av Azure Database Migration Service.
4. Skapa ett migreringsjobb som anger käll databas (er), mål databaser och de tabeller som ska migreras.
5. Starta den fullständiga belastningen.
6. Välj den efterföljande verifieringen.
7. Utför en manuell växling av produktions miljön till den nya molnbaserade databasen.

## <a name="troubleshooting-and-optimization"></a>Fel sökning och optimering

**C. Jag konfigurerar ett migreringsjobb i DMS och jag har problem med att ansluta till min käll databas. Vad ska jag göra?**
Om du har problem med att ansluta till käll databas systemet medan du arbetar med migreringen, skapar du en virtuell dator i samma undernät i det virtuella nätverket där du konfigurerar din DMS-instans. På den virtuella datorn bör du kunna köra ett anslutnings test, till exempel använda en UDL-fil för att testa en anslutning till SQL Server eller ladda ned Robo-3T för att testa MongoDB-anslutningar. Om anslutnings testet lyckas bör du inte ha problem med att ansluta till käll databasen. Kontakta nätverks administratören om anslutnings testet inte lyckas.

**C. Varför är mina Azure Database Migration Service otillgängliga eller stoppade?**
Om användaren uttryckligen stoppar Azure Database Migration Service (DMS) eller om tjänsten är inaktiv under en period på 24 timmar, kommer tjänsten att vara i ett stoppat eller automatiskt pausat läge. I varje fall är tjänsten inte tillgänglig och har statusen stoppad.  Starta om tjänsten för att återuppta aktiva migreringar.

**C. Finns det några rekommendationer för att optimera prestanda för Azure Database Migration Service?**
Du kan göra några saker för att påskynda migreringen av databasen med hjälp av tjänsten:

* Använd pris nivån för flera CPU-Generell användning när du skapar tjänst instansen så att tjänsten kan dra nytta av flera virtuella processorer för parallellisering och snabbare data överföring.
* Skala tillfälligt upp Azure SQL Database mål instansen till SKU: n för Premium nivån under datamigreringen för att minimera Azure SQL Database begränsning som kan påverka data överförings aktiviteter när du använder lager på lägre nivåer.

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Azure Database migration service](dms-overview.md).
