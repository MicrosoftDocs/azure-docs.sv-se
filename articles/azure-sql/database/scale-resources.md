---
title: Skala resurser
description: Den här artikeln förklarar hur du skalar databasen i Azure SQL Database-och SQL-hanterad instans genom att lägga till eller ta bort allokerade resurser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: ca1a2edec70b13f111ffd89278aa39d1ddea7f67
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035650"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Skala databas resurser dynamiskt med minimal stillestånds tid
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure SQL Database-och SQL-hanterad instans kan du dynamiskt lägga till fler resurser i databasen med minimal [stillestånds](https://azure.microsoft.com/support/legal/sla/sql-database)tid. Det finns dock en växel över tid där anslutningen förloras till databasen under en kort tids period, vilket kan minskas med logiken för omprövning.

## <a name="overview"></a>Översikt

När efter frågan på din app växer från en fåtal enheter och kunder till miljon tals, Azure SQL Database och SQL-hanterad instans skala i farten med minimal stillestånds tid. Skalbarhet är en av de viktigaste egenskaperna för PaaS (Platform as a Service) som gör det möjligt att dynamiskt lägga till fler resurser till tjänsten när det behövs. Med Azure SQL Database kan du enkelt ändra resurser (processor kraft, minne, IO-dataflöde och lagring) som allokerats till dina databaser.

Du kan minska prestanda problem på grund av ökad användning av programmet som inte kan åtgärdas med hjälp av metoder för indexering eller fråga om omskrivning. Genom att lägga till fler resurser kan du snabbt reagera när databasen träffar de aktuella resurs gränserna och behöver mer kraft för att hantera inkommande arbets belastning. Med Azure SQL Database kan du också skala ut resurserna när de inte behövs för att sänka kostnaderna.

Du behöver inte bekymra dig om att köpa maskin vara och ändra den underliggande infrastrukturen. Det går lätt att skala en databas via Azure Portal med hjälp av ett skjutreglage.

![Skalning av databas prestanda](./media/scale-resources/scale-performance.svg)

Azure SQL Database erbjuder den [DTU-baserade inköps modellen](service-tiers-dtu.md) och den [vCore-baserade inköps modellen](service-tiers-vcore.md), medan Azure SQL Managed instance bara erbjuder den [vCore-baserade inköps modellen](service-tiers-vcore.md). 

- Den [DTU-baserade inköps modellen](service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes-och i/O-resurser i tre tjänst nivåer för att stödja lätta till tunga databas arbets belastningar: Basic, standard och Premium. Det finns prestandanivåer inom varje nivå med en blandning av dessa resurser, och du kan lägga till ytterligare lagringsresurser till dessa.
- Med den [vCore-baserade inköps modellen](service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden eller minnet samt mängden och lagrings hastigheten. Den här inköps modellen erbjuder tre tjänst nivåer: Generell användning, Affärskritisk och storskalig.

Du kan bygga din första app på en liten, enkel databas till en låg kostnad per månad på nivån Basic, standard eller Generell användning och sedan ändra dess tjänst nivå manuellt eller program mässigt när som helst till Premium-eller Affärskritisk Service nivån för att uppfylla behoven hos din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

> [!NOTE]
> Dynamisk skalbarhet skiljer sig från autoskalning. Autoskalning är när en tjänst skalas automatiskt baserat på villkor, medan dynamisk skalbarhet möjliggör manuell skalning med minimal stillestånds tid.

Enkla databaser i Azure SQL Database stöder manuell dynamisk skalbarhet, men inte autoskalning. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov.
Det finns dock skript som kan hjälpa dig att automatisera skalbarheten för en enskild databas i Azure SQL Database. Mer information finns i [Använd PowerShell till att övervaka och skala en enskild SQL-databas](scripts/monitor-and-scale-database-powershell.md).

Du kan ändra [DTU-tjänstens nivåer](service-tiers-dtu.md) eller [vCore-egenskaper](resource-limits-vcore-single-databases.md) när som helst med minimal stilleståndstid för ditt program (ofta under fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här scenariot använder du en elastisk pool med ett visst antal eDTU: er som delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/scale-resources/single_db_dtus.png)

Azure SQL Database ger möjlighet att dynamiskt skala dina databaser:

- Med en [enda databas](single-database-scale.md)kan du använda antingen [DTU](resource-limits-dtu-single-databases.md) -eller [vCore](resource-limits-vcore-single-databases.md) -modeller för att definiera maximalt antal resurser som ska tilldelas varje databas.
- [Elastiska pooler](elastic-pool-scale.md) gör att du kan definiera högsta resurs gräns per grupp av databaser i poolen.

Med Azure SQL Managed instance kan du skala även: 

- [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) använder [virtuella kärnor](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) -läge och gör att du kan definiera högsta processor kärnor och maximalt lagrings utrymme som allokeras till din instans. Alla databaser i den hanterade instansen delar resurser som är allokerade till instansen.

Att initiera åtgärden för att skala upp eller ned i någon av varianter startar databas motor processen och flyttar den till en annan virtuell dator om det behövs. Processen att flytta databas motorn till en ny virtuell dator är en **online process** där du kan fortsätta att använda din befintliga Azure SQL Database-tjänst medan processen pågår. När mål databas motorn är fullständigt initierad och redo att bearbeta frågorna växlar anslutningarna [från källa till mål databas motorn](single-database-scale.md#impact).

> [!NOTE]
> Vi rekommenderar inte att du skalar den hanterade instansen om en tids krävande transaktion, till exempel data import, data bearbetnings jobb, index återuppbyggnad osv., körs eller om du har en aktiv anslutning på instansen. För att förhindra att skalningen tar längre tid att slutföra än vanligt, bör du skala instansen när alla långvariga åtgärder har slutförts.

> [!NOTE]
> Du kan vänta en kort anslutnings rast när processen för att skala upp/skala ned är färdig. Om du har implementerat [omprövnings logik för vanliga tillfälliga fel](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), ser du inte redundansväxlingen.

## <a name="alternative-scale-methods"></a>Alternativa skalnings metoder

Skalning av resurser är det enklaste och mest effektiva sättet att förbättra databasens prestanda utan att ändra antingen databas-eller program koden. I vissa fall kan det hända att även de högsta tjänst nivåerna, beräknings storlekarna och prestanda optimeringen inte hanterar din arbets belastning på ett framgångs rika och kostnads effektivt sätt. I så fall har du följande ytterligare alternativ för att skala databasen:

- [Läs skalning](read-scale-out.md) är en tillgänglig funktion där du får en skrivskyddad replik av dina data där du kan köra krävande skrivskyddade frågor som rapporter. En skrivskyddad replik hanterar din skrivskyddade arbets belastning utan att påverka resursanvändningen på din primära databas.
- [Database horisontell partitionering](elastic-scale-introduction.md) är en uppsättning tekniker som du kan använda för att dela upp data i flera databaser och skala dem oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

- Information om hur du kan förbättra databasens prestanda genom att ändra databas koden finns i [hitta och tillämpa prestanda rekommendationer](database-advisor-find-recommendations-portal.md).
- Information om hur du kan optimera databasen med inbyggd databas information finns i [Automatisk justering](automatic-tuning-overview.md).
- Information om Läs skalbarhet i Azure SQL Database finns i så här använder du [skrivskyddade repliker för att belastningsutjämna skrivskyddade arbets belastningar för frågor](read-scale-out.md).
- Information om en databas-horisontell partitionering finns i [skala ut med Azure SQL Database](elastic-scale-introduction.md).
