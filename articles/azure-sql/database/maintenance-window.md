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
ms.date: 03/05/2021
ms.openlocfilehash: b658fa9f2df6e8a88df89f9e8ccc1cf6b68cec39
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426067"
---
# <a name="maintenance-window-preview"></a>Underhålls period (för hands version)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med funktionen underhålls fönster kan du konfigurera ett underhålls schema för [Azure SQL Database](sql-database-paas-overview.md) -och [Azure SQL-hanterade instans](../managed-instance/sql-managed-instance-paas-overview.md) resurser som gör det förutsägbara underhålls händelser och mindre störande för arbets belastningen. 

> [!Note]
> Underhålls fönster funktionen skyddar inte mot oplanerade händelser, t. ex. maskin varu fel, vilket kan orsaka korta anslutnings avbrott.

## <a name="overview"></a>Översikt

Azure utför regelbundet [planerat underhåll](planned-maintenance.md) av SQL Database-och SQL-hanterade instans resurser. Under Azure SQL Maintenance-händelsen är databaser helt tillgängliga, men de kan vara underkastade korta redundans i respektive tillgänglighets service avtal för [SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) -och [SQL-hanterad instans](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance), eftersom omkonfiguration av resurser krävs i vissa fall.

Underhålls perioden är avsedd för produktions arbets belastningar som inte är elastiska för databas-eller instans växlingar och inte kan absorbera korta anslutnings avbrott som orsakas av planerade underhålls händelser. Genom att välja ett underhålls fönster som du föredrar kan du minimera effekten av planerat underhåll eftersom det sker utanför arbets tiden. Elastiska arbets belastningar och icke-produktions arbets belastningar kan förlita sig på Azure SQLs standard underhålls princip.

Underhålls perioden kan konfigureras vid skapande eller för befintliga Azure SQL-resurser. Den kan konfigureras med hjälp av API: et för Azure Portal, PowerShell, CLI eller Azure.

> [!Important]
> Att konfigurera underhålls perioden är en tids krävande asynkron åtgärd, ungefär som att ändra tjänst nivån för Azure SQL-resursen. Resursen är tillgänglig under åtgärden, förutom en kort redundansväxling som sker i slutet av åtgärden och som vanligt vis varar i upp till åtta sekunder, även om tids krävande transaktioner har avbrutits. För att minimera effekten av redundans bör du utföra åtgärden utanför det högsta antalet timmar.

### <a name="gain-more-predictability-with-maintenance-window"></a>Få mer förutsägbart med underhålls perioden

Som standard blockerar Azure SQL Maintenance-principen påverkan på uppdateringar under perioden **8.00 till 17 lokal tid varje dag** för att undvika eventuella avbrott under perioder med normal arbets tid. Lokal tid bestäms av platsen för den [Azure-region](https://azure.microsoft.com/global-infrastructure/geographies/) som är värd för resursen och kan observera sommar tid i enlighet med den lokala tids zons definitionen. 

Du kan ändra underhålls uppdateringar ytterligare till en tid som är lämplig för dina Azure SQL-resurser genom att välja mellan två ytterligare underhålls fönster platser:
 
* Vardags fönstret, 10PM till 06:00 lokal tid måndag-torsdag
* Helg fönstret, 10PM till 06:00 Local Time fredag – söndag

När du har valt underhålls perioden och slutfört tjänst konfigurationen sker planerat underhåll endast under det fönster du väljer.   

> [!Important]
> I sällsynta fall där eventuella senareläggningar av åtgärder kan orsaka allvarlig påverkan, som att tillämpa viktiga säkerhets korrigeringar, kan konfigurerade underhålls perioder tillfälligt åsidosättas. 

### <a name="cost-and-eligibility"></a>Kostnader och berättigande

Att konfigurera och använda underhålls perioden är kostnads fritt för alla [typer](https://azure.microsoft.com/support/legal/offer-details/)av berättigade erbjudanden: betala per användning, CSP (Cloud Solution Provider), Microsoft Enterprise-avtal eller Microsofts kund avtal.

> [!Note]
> Ett Azure-erbjudande är den typ av Azure-prenumeration som du har. Till exempel är en prenumeration med [priser enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/)användning, [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p/)och [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) alla Azure-erbjudanden. Varje erbjudande eller plan har olika villkor och fördelar. Ditt erbjudande eller din plan visas i prenumerationens översikt. Mer information om hur du byter prenumeration på ett annat erbjudande finns i [ändra din Azure-prenumeration till ett annat erbjudande](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Avancerade aviseringar

Underhålls aviseringar kan konfigureras för att varna dig om kommande planerade underhålls händelser för din Azure SQL Database 24 timmar i förväg, vid tidpunkten för underhållet och när underhållet har slutförts. Mer information finns i [förskotts aviseringar](advance-notifications.md).

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

## <a name="considerations-for-azure-sql-managed-instance"></a>Överväganden för Azure SQL-hanterad instans

Azure SQL-hanterad instans består av tjänst komponenter som finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverk under nät. De här virtuella datorerna utgör ett [eller flera virtuella kluster](/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) som kan vara värdar för flera hanterade instanser. Underhålls fönstret som kon figurer ATS på instanser av ett undernät kan påverka antalet virtuella kluster i under nätet och distributionen av instanser mellan virtuella kluster. Detta kan kräva en bedömning av få effekter.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Underhålls periodens konfiguration är tids krävande åtgärd 
Alla instanser som finns i ett virtuellt kluster delar underhålls perioden. Som standard finns alla hanterade instanser i det virtuella klustret med standard underhålls perioden. Om du anger ett annat underhålls fönster för en hanterad instans under skapandet eller efteråt, innebär det att det måste placeras i det virtuella klustret med motsvarande underhålls period. Om det inte finns något sådant virtuellt kluster i under nätet måste du först skapa ett nytt för att rymma instansen. Om du tar emot ytterligare instanser i det befintliga virtuella klustret kan du behöva ändra storlek på klustret. Båda åtgärderna bidrar till att konfigurera underhålls periodens varaktighet för en hanterad instans.
Förväntad varaktighet för konfigurering av underhålls perioden på den hanterade instansen kan beräknas med [Beräknad varaktighet för instans hanterings åtgärder](/azure/azure-sql/managed-instance/management-operations-overview#duration).

> [!Important]
> En kort redundansväxling sker i slutet av underhålls åtgärden och tar vanligt vis upp till åtta sekunder, även om tids krävande transaktioner har avbrutits. För att minimera effekten av redundans bör du schemalägga åtgärden utanför det högsta antalet timmar.

### <a name="ip-address-space-requirements"></a>Krav för IP-adressutrymme
Varje nytt virtuellt kluster i under nätet kräver ytterligare IP-adresser enligt [tilldelningen av virtuella kluster-IP](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size)-adresser. Att ändra underhålls period för befintlig hanterad instans kräver också [tillfällig ytterligare IP-kapacitet](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) som vid skalning av virtuella kärnor-scenariot för motsvarande tjänst nivå.

### <a name="ip-address-change"></a>Ändring av IP-adress
När du konfigurerar och ändrar underhålls fönstret ändras instansens IP-adress inom under nätets IP-adressintervall.

> [!Important]
>  Kontrol lera att NSG-och brand Väggs reglerna inte blockerar data trafik när IP-adressen ändras. 

## <a name="next-steps"></a>Nästa steg

* [Avancerade aviseringar](advance-notifications.md)
* [Konfigurera underhålls period](maintenance-window-configure.md)

## <a name="learn-more"></a>Läs mer

* [Vanliga frågor och svar om underhålls fönstret](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planera för Azures underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans](planned-maintenance.md)





