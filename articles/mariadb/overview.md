---
title: Översikt – Azure Database for MariaDB
description: Lär dig mer om tjänsten Azure Database for MariaDB, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 7c365dee2b3ac32a62b38d0b3273f2f13e402936
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662025"
---
# <a name="what-is-azure-database-for-mariadb"></a>Vad är Azure Database for MariaDB?

Azure Database for MariaDB är en relationsdatabastjänst i Microsofts moln. Azure Database for MariaDB baseras på [MariaDB Community Edition](https://mariadb.org/download/) (finns i GPLv2-licensen) databas motor, version 10,2 och 10,3.

Med Azure Database for MariaDB får du följande:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med inkluderande Betala per användning-prissättning.
- Skalning efter behov inom några sekunder.
- Skydd av känsliga data i vila och i rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkter i upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan ingen administration. De tillhandahålls utan extra kostnad. Azure Database for MariaDB kan hjälpa dig att snabbt utveckla din app och förkorta tiden till marknaden. Du behöver inte allokera dyrbar tid och viktiga resurser för att hantera virtuella datorer och infrastruktur. Du kan även fortsätta att utveckla programmet med hjälp av de verktyg med öppen källkod och den plattform du väljer. Leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

Mer information om de grundläggande begreppen och funktionerna i Azure Database for MariaDB, inklusive prestanda, skalbarhet och hanterbarhet, finns i de här snabbstarterna:

- [Skapa en Azure Database for MariaDB-server med Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Skapa en Azure Database for MariaDB-server med Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Tjänsten Azure Database for MariaDB erbjuder flera tjänstnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet hjälper databasen att reagera transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Hur avgör du när du ska skapa upp eller ned? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering i Azure Database for MariaDB, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. Mer information finns i [Aviseringar](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet

Azures branschledande serviceavtal med 99,99 % tillgänglighet drivs av ett globalt nätverk med Microsoft-hanterade datacenter. Nätverket hjälper din app att köras dygnet runt. Du kan dra nytta av inbyggda funktioner för säkerhet, feltolerans och dataskydd i Azure Database for MariaDB. Med Azure Database for MariaDB kan du använda point-in-time-återställning för att återställa en server till ett tidigare skede, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data

Azure-databastjänster har en tradition av datasäkerhet som Azure Database for MariaDB upprätthåller. Azure Database for MariaDB ger funktioner som begränsar åtkomst, skyddar data i vila och under rörelse samt hjälper dig att övervaka aktivitet. Besök [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/security) för information om Azures plattformssäkerhet. Mer information om Azure Database for MariaDB säkerhetsfunktioner finns i [säkerhets översikten](concepts-security.md).

## <a name="contacts"></a>Kontakter

Du kan skicka eventuella frågor eller förslag som du har om att arbeta med Azure Database for MariaDB till [Azure Database for MariaDB-teamet](mailto:AskAzureDBforMariaDB@service.microsoft.com) (det är inte ett alias för teknisk support).

Du kan även använda följande kontaktpunkter:
- För att kontakta Azure-supporten [öppnar du en supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i Azure-portalen.
- Om du vill åtgärda ett problem med ditt konto [öppnar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner skapar du ett inlägg på [Azure-feedbackforumen](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Nästa steg

Nu när du har läst en introduktion till Azure Database for MariaDB är du redo att:
- Se [pris](https://azure.microsoft.com/pricing/details/mariadb/) sidan för kostnads jämförelser och räknare. 
- Kom igång genom [att skapa din första server](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
