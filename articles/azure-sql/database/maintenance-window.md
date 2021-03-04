---
title: Underhållsperiod
description: Lär dig hur underhålls fönstret Azure SQL Database och hanterade instanser kan konfigureras.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/04/2021
ms.openlocfilehash: 0a9a4b2de03c62640bb1c643d3ff3da4139d42a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101213"
---
# <a name="maintenance-window-preview"></a>Underhålls period (för hands version)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funktionen underhålls fönster gör att du kan konfigurera scheman för förväntat underhålls fönster för [Azure SQL Database](sql-database-paas-overview.md) -och [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md). 

Mer information om underhålls händelser finns i [Planera för Azure underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans](planned-maintenance.md).

## <a name="overview"></a>Översikt

Azure utför planerade underhålls uppdateringar på Azure SQL Database-och SQL-hanterade instans resurser med jämna mellanrum som ofta omfattar uppdateringar av underliggande maskin vara, program inklusive underliggande operativ system (OS) och SQL-motorn. Under en underhålls uppdatering är resurserna fullständigt tillgängliga och tillgängliga, men vissa av underhålls uppdateringarna kräver en redundansväxling när Azure tar instanser offline under en kort tid för att tillämpa underhålls uppdateringar (åtta sekunder i varaktigheten i genomsnitt).  Planerade underhålls uppdateringar sker var 35: e dag i genomsnitt, vilket innebär att kunden kan vänta ungefär en planerad underhålls händelse per månad per Azure SQL Database eller SQL-hanterad instans, och endast under underhålls periodens platser som valts av kunden.   

Underhålls perioden är avsedd för företags arbets belastningar som inte är elastiska till tillfälliga anslutnings problem som kan leda till planerade underhålls händelser.

Underhålls perioden kan konfigureras med hjälp av Azure Portal, PowerShell, CLI eller Azure API. Den kan konfigureras vid skapande eller för befintliga SQL-databaser och SQL-hanterade instanser.

> [!Important]
> Att konfigurera underhålls perioden är en tids krävande asynkron åtgärd, ungefär som att ändra tjänst nivån för Azure SQL-resursen. Resursen är tillgänglig under åtgärden, förutom en kort redundansväxling som sker i slutet av åtgärden och som vanligt vis varar i upp till åtta sekunder, även om tids krävande transaktioner har avbrutits. För att minimera effekten av redundans bör du utföra åtgärden utanför det högsta antalet timmar.

### <a name="gain-more-predictability-with-maintenance-window"></a>Få mer förutsägbart med underhålls perioden

Som standard uppdateras alla Azure SQL-databaser och hanterade instans databaser endast under 17 till 8.00 dygnet för att undvika avbrott i arbets tiden. Lokal tid bestäms av den [Azure-region](https://azure.microsoft.com/global-infrastructure/geographies/) som är värd för resursen. Du kan ändra underhålls uppdateringar ytterligare till en tid som är lämplig för din databas genom att välja mellan två ytterligare underhålls fönster platser:
 
* Vardags fönstret, 10PM till 06:00 lokal tid måndag – torsdag
* Helg fönstret, 10PM till 06:00 Local Time fredag – söndag

När valet av underhålls fönster har gjorts och tjänst konfigurationen har slutförts sker alla planerade underhålls uppdateringar bara under det fönster du väljer.   

> [!Note]
> Förutom planerade underhålls uppdateringar kan i sällsynta fall oplanerade underhålls händelser orsaka otillgänglighet. 

### <a name="cost-and-eligibility"></a>Kostnader och berättigande

Att konfigurera och använda underhålls perioden är kostnads fritt för alla [typer](https://azure.microsoft.com/support/legal/offer-details/)av berättigade erbjudanden: betala per användning, CSP (Cloud Solution Provider), Microsoft Enterprise eller Microsofts kund avtal.

> [!Note]
> Ett Azure-erbjudande är den typ av Azure-prenumeration som du har. Till exempel är en prenumeration med [priser enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/)användning, [Azure i Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)och [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) alla Azure-erbjudanden. Varje erbjudande eller plan har olika villkor och fördelar. Ditt erbjudande eller din plan visas i prenumerationens översikt. Mer information om hur du byter prenumeration på ett annat erbjudande finns i [ändra din Azure-prenumeration till ett annat erbjudande](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Avancerade aviseringar

Underhålls aviseringar kan konfigureras för att varna dig om kommande planerade underhålls händelser för dig Azure SQL Database 24 timmar i förväg, vid tidpunkten för underhållet och när underhålls perioden är slutförd. Mer information finns i [förskotts aviseringar](advance-notifications.md).

## <a name="availability"></a>Tillgänglighet

### <a name="supported-service-level-objectives"></a>Service nivå mål som stöds

Att välja ett annat underhålls fönster än standardinställningen är tillgängligt på alla SLO: erna **utom**:
* Hyperskala 
* Instanspooler
* Äldre Gen4-vCore
* Basic, S0 och S1 
* DC, Fsv2, M-serien

### <a name="azure-region-support"></a>Stöd för Azure-regioner

Att välja ett annat underhålls fönster än standard är för närvarande tillgängligt i följande regioner:

- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, centrala
- Central US
- East US
- USA, östra 2
- Japan, östra
- NorthCentral oss
- Europa, norra
- SouthCentral oss
- Asien, Sydostasien
- Storbritannien, södra
- Europa, västra
- USA, västra
- USA, västra 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Gateway-underhåll för Azure SQL Database

För att få maximal nytta av underhålls perioder kontrollerar du att klient programmen använder anslutnings principen för omdirigering. Omdirigera är den rekommenderade anslutnings principen, där klienter upprättar anslutningar direkt till noden som är värd för databasen, vilket leder till minskad svars tid och bättre data flöde.  

* I Azure SQL Database kan alla anslutningar som använder proxy-anslutningssträngen påverkas av både det valda underhålls fönstret och en underhålls period för gateway Node. Klient anslutningar som använder den rekommenderade principen för att omdirigera anslutningar påverkas dock inte av en gateway-nod underhåll redundans. 

* I Azure SQL-hanterad instans finns Gateway-noderna [i det virtuella klustret](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) och har samma underhålls fönster som den hanterade instansen, men att använda anslutnings principen för omdirigering rekommenderas fortfarande för att minimera antalet avbrott under underhålls händelsen.

Mer information om klient anslutnings principen i Azure SQL Database finns [Azure SQL Database anslutnings princip](../database/connectivity-architecture.md#connection-policy). 

Mer information om klient anslutnings principen i Azure SQL-hanterad instans finns i [anslutnings typer för Azure SQL-hanterad instans](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Nästa steg

* [Avancerade aviseringar](advance-notifications.md)
* [Konfigurera underhålls period](maintenance-window-configure.md)

## <a name="learn-more"></a>Läs mer

* [Vanliga frågor och svar om underhålls fönstret](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planera för Azures underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans](planned-maintenance.md)




