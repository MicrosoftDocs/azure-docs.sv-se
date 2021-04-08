---
title: Skala ut
description: SaaS-utvecklare (Software as a Service) kan enkelt skapa elastiska och skalbara databaser i molnet med hjälp av dessa verktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3f00b2c1a8a8264267aa8ae68d80890adeb642ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059153"
---
# <a name="scaling-out-with-azure-sql-database"></a>Skala ut med Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Du kan enkelt skala ut databaser i Azure SQL Database med hjälp av **Elastic Database** verktyg. Med dessa verktyg och funktioner kan du använda databas resurser i **Azure SQL Database** för att skapa lösningar för transaktions arbets belastningar och särskilt program vara som en tjänst (SaaS) program. Elastic Database funktioner består av:

* [Elastic Database klient bibliotek](elastic-database-client-library.md): klient biblioteket är en funktion som gör att du kan skapa och underhålla shardade-databaser.  Se [Kom igång med Elastic Database verktyg](elastic-scale-get-started.md).
* [Elastic Database Split-Merge-verktyget](elastic-scale-overview-split-and-merge.md): flyttar data mellan shardade-databaser. Det här verktyget är användbart för att flytta data från en databas med flera klienter till en databas för en enskild klient (eller vice versa). Se [själv studie kursen om elastiska databas Split-Merge](elastic-scale-configure-deploy-split-and-merge.md).
* [Elastic Database-jobb](elastic-jobs-overview.md) (för hands version): Använd jobb för att hantera ett stort antal databaser i Azure SQL Database. Du kan enkelt utföra administrativa åtgärder, till exempel schema ändringar, hantering av autentiseringsuppgifter, referens data uppdateringar, insamling av prestanda data eller klient (Customer) telemetri med hjälp av jobb.
* [Elastic Database fråga](elastic-query-overview.md) (för hands version): gör att du kan köra en Transact-SQL-fråga som sträcker sig över flera databaser. Detta möjliggör anslutning till rapporterings verktyg som Excel, Power BI, Tableau osv.
* [Elastiska transaktioner](elastic-transactions-overview.md): med den här funktionen kan du köra transaktioner som sträcker sig över flera databaser. Elastic Database-transaktioner är tillgängliga för .NET-program med ADO .NET och integreras med den välbekanta programmerings upplevelsen med [system. Transaction-klasser](/dotnet/api/system.transactions).

Följande bild visar en arkitektur som innehåller **Elastic Database funktioner** i relation till en samling databaser.

I den här bilden representerar färger i databasen scheman. Databaser med samma färg delar samma schema.

1. En uppsättning **SQL-databaser** finns i Azure med hjälp av horisontell partitionering-arkitekturen.
2. **Elastic Database klient biblioteket** används för att hantera en Shard-uppsättning.
3. En delmängd av databaserna placeras i en **elastisk pool**. (Se [Vad är en pool?](elastic-pool-overview.md)).
4. Ett **Elastic Database jobb** kör schemalagda eller ad hoc T-SQL-skript mot alla databaser.
5. **Verktyget Dela-merge** används för att flytta data från en Shard till en annan.
6. Med den **Elastic Database frågan** kan du skriva en fråga som omfattar alla databaser i Shard-uppsättningen.
7. Med **elastiska transaktioner** kan du köra transaktioner som sträcker sig över flera databaser. 

![Elastic Database-verktyg][1]

## <a name="why-use-the-tools"></a>Varför ska jag använda verktygen?

Att uppnå elastiskhet och skala för moln program har varit enkelt för virtuella datorer och Blob Storage – Lägg bara till eller dra ifrån enheter eller öka strömförbrukningen. Men det har varit en utmaning för tillstånds känslig data bearbetning i Relations databaser. Utmaningarna i följande scenarier:

* Ökande och krympnings kapacitet för Relations databasen i din arbets belastning.
* Hantering av hotspots som kan uppstå påverkar en viss delmängd av data, t. ex. en upptaget slut kund (klient).

Traditionellt sätt har scenarier som dessa har åtgärd ATS genom att investera i större skalade servrar för att stödja programmet. Det här alternativet är dock begränsat i molnet där all bearbetning sker på fördefinierad maskin vara. Distribuera data och bearbetning över flera identiskt strukturerade databaser (ett skalbart mönster som kallas "horisontell partitionering"), vilket är ett alternativ till traditionella skalnings metoder både vad gäller kostnad och elastiskhet.

## <a name="horizontal-and-vertical-scaling"></a>Vågrät och lodrät skalning

Följande bild visar de vågräta och lodräta dimensionerna för skalning, som är de grundläggande sätt som de elastiska databaserna kan skalas på.

![Vågrätt och lodrätt skala ut][2]

Horisontell skalning syftar på att lägga till eller ta bort databaser för att justera kapaciteten eller den övergripande prestandan, även kallat "skala ut". Horisontell partitionering, där data partitioneras i en samling med identiskt strukturerade databaser, är ett vanligt sätt att implementera horisontell skalning.  

Vertikal skalning syftar på att öka eller minska beräknings storleken för en enskild databas, även kallat "skala upp".

De flesta databas program i moln skala använder en kombination av dessa två strategier. Till exempel kan en program vara som ett tjänst program använda horisontell skalning för att etablera nya slut kunder och lodrät skalning för att tillåta varje slutkunds databas att växa eller minska resurser efter behov av arbets belastningen.

* Horisontell skalning hanteras med [Elastic Database klient biblioteket](elastic-database-client-library.md).
* Vertikal skalning görs med hjälp av Azure PowerShell-cmdletar för att ändra tjänst nivån, eller genom att placera databaser i en elastisk pool.

## <a name="sharding"></a>Horisontell partitionering

*Horisontell partitionering* är en teknik för att distribuera stora mängder identiskt strukturerade data över flera oberoende databaser. Det är särskilt populärt med moln utvecklare som skapar SAAS-erbjudanden (Software as a Service) för slutanvändare eller företag. Dessa slut kunder kallas ofta "klienter". Horisontell partitionering kan krävas av valfritt antal orsaker:  

* Den totala mängden data är för stor för att rymmas inom en enskild Databass begränsningar
* Transaktions flödet för den övergripande arbets belastningen överskrider funktionerna i en enskild databas
* Klienter kan behöva fysisk isolering från varandra, så separata databaser behövs för varje klient
* Olika delar av en databas kan behöva finnas i olika geografiska områden för efterlevnad, prestanda eller geografiskt politiska skäl.

I andra scenarier, t. ex. inmatning av data från distribuerade enheter, kan horisontell partitionering användas för att fylla i en uppsättning databaser som är ordnade temporalt. En separat databas kan till exempel vara dedikerad till varje dag eller vecka. I så fall kan horisontell partitionering-nyckeln vara ett heltal som representerar datumet (finns i alla rader i shardade-tabellerna) och frågor som hämtar information om ett datum intervall måste dirigeras av programmet till den delmängd databaser som omfattar intervallet i fråga.

Horisontell partitionering fungerar bäst när varje transaktion i ett program kan begränsas till ett enda värde för en horisontell partitionering-nyckel. Detta säkerställer att alla transaktioner är lokala för en speciell databas.

## <a name="multi-tenant-and-single-tenant"></a>Flera innehavare och en enda klient

Vissa program använder den enklaste metoden för att skapa en separat databas för varje klient. Den här metoden är ett **horisontell partitionering-mönster för en innehavare** som ger isolerings-, säkerhets kopierings-/återställnings möjligheter och resurs skalning på klientens granularitet. Med en enda klient horisontell partitionering är varje databas kopplad till ett visst klient-ID-värde (eller kund nyckel värde), men den nyckeln behöver inte alltid finnas i själva data. Det är programmets ansvar att dirigera varje begäran till lämplig databas – och klient biblioteket kan förenkla detta.

![En enda klient eller flera innehavare][4]

Andra scenarier packar flera klienter tillsammans i databaser, i stället för att isolera dem i separata databaser. Det här mönstret är ett typiskt **horisontell partitionering-mönster för flera innehavare** – och det kan drivas av det faktum att ett program hanterar ett stort antal små klienter. I horisontell partitionering för flera innehavare är raderna i databas tabellerna utformade för att innehålla en nyckel som identifierar klient-ID: t eller horisontell partitionering-nyckeln. På nytt är program nivån ansvarig för att dirigera en klients förfrågan till lämplig databas och detta kan stödjas av klient biblioteket för Elastic Database. Dessutom kan säkerhet på radnivå användas för att filtrera vilka rader varje klient kan komma åt – mer information finns i [program med flera klienter med elastiska databas verktyg och säkerhet på radnivå](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md). Du kan behöva distribuera om data mellan databaser med horisontell partitionering-mönstret för flera innehavare och under lätta för verktyget för delning och sammanslagning av elastiska databaser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](saas-tenancy-app-design-patterns.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Flytta data från flera till databaserna med en enda innehavare
När du skapar ett SaaS-program är det vanligt att erbjuda potentiella kunder en utvärderings version av program varan. I det här fallet är det kostnads effektivt att använda en databas för flera innehavare för data. Men när en potentiell kund blir en kund är en databas för en enda klient bättre eftersom den ger bättre prestanda. Om kunden hade skapat data under utvärderings perioden använder du [verktyget Dela-sammanslagning](elastic-scale-overview-split-and-merge.md) för att flytta data från flera klienter till den nya databasen med en enda klient.

## <a name="next-steps"></a>Nästa steg
Ett exempel på en app som demonstrerar klient biblioteket finns i [Kom igång med Elastic Database verktyg](elastic-scale-get-started.md).

Om du vill konvertera befintliga databaser till att använda verktygen, se [migrera befintliga databaser för att skala ut](elastic-convert-to-use-elastic-tools.md).

Om du vill se information om den elastiska poolen läser du [pris-och prestanda överväganden för en elastisk pool](elastic-pool-overview.md)eller skapar en ny pool med [elastiska pooler](elastic-pool-manage.md).  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png
