---
title: Kör Azure Data Lake U-SQL-skript på din lokala dator
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio för att köra U-SQL-jobb på den lokala datorn.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018995"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Kör U-SQL-skript på din lokala dator

När du utvecklar U-SQL-skript kan du spara tid och pengar genom att köra skripten lokalt. Azure Data Lake verktyg för Visual Studio stöder körning av U-SQL-skript på den lokala datorn. 

## <a name="basic-concepts-for-local-runs"></a>Grundläggande begrepp för lokala körningar

Följande diagram visar komponenterna för lokal körning och hur dessa komponenter mappas till moln körning.

|Komponent|Lokal körning|Moln körning|
|---------|---------|---------|
|Storage|Lokal rotmapp|Standard Azure Data Lake Stores konto|
|Compute|Lokal U-SQL-körnings motor|Azure Data Lake Analytics tjänst|
|Kör miljö|Arbets katalog på lokal dator|Azure Data Lake Analytics kluster|

I avsnitten nedan finns mer information om lokala kör komponenter.

### <a name="local-data-root-folders"></a>Lokala data rot mappar

En lokal datarotmapp är en **lokal lagrings plats** för det lokala beräknings kontot. Alla mappar i det lokala fil systemet på den lokala datorn kan vara en lokal datarotmapp. Det är samma som standard Azure Data Lake Stores kontot för ett Data Lake Analytics-konto. Växling till en annan datarotmapp är precis som att växla till ett annat standard lagrings konto. 

Rotmappen för data används på följande sätt:
- Lagra metadata. Exempel är databaser, tabeller, tabell värdes funktioner och sammansättningar.
- Leta upp de indata och utdata som definieras som relativa sökvägar i U-SQL-skript. Genom att använda relativa sökvägar är det enklare att distribuera U-SQL-skript till Azure.

### <a name="u-sql-local-run-engines"></a>Lokala U-SQL-körnings motorer

En lokal U-SQL-körnings motor är ett **lokalt beräknings konto** för U-SQL-jobb. Användare kan köra U-SQL-jobb lokalt via Azure Data Lake verktyg för Visual Studio. Lokala körningar stöds också via Azure Data Lake U-SQL SDK kommando rads-och programmerings gränssnitt. Läs mer om [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Arbets kataloger

När du kör ett U-SQL-skript krävs en arbetskatalog-mapp för att cachelagra kompileringsfel, köra loggar och utföra andra funktioner. I Azure Data Lake verktyg för Visual Studio är arbets katalogen U-SQL-projektets arbets katalog. Den finns under `<U-SQL project root path>/bin/debug>` . Arbets katalogen rensas varje gång en ny körning utlöses.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokala körningar i Microsoft Visual Studio

Azure Data Lake Tools för Visual Studio har en inbyggd lokal körnings motor. Verktygs ytan är motorn som ett lokalt beräknings konto. Om du vill köra ett U-SQL-skript lokalt väljer du kontot **lokalt** eller **lokalt projekt** på den nedrullningsbara menyn redigerings marginal för skriptet. Välj sedan **Skicka**.

![Skicka ett U-SQL-skript till ett lokalt konto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokala körningar med ett lokalt dator konto

Ett **lokalt dator** konto är ett delat lokalt data beräknings konto med en enda lokal datarotmapp som det lokala lagrings kontot. Som standard finns rotmappen på **C:\Users \<username> \AppData\Local\USQLDataRoot**. Den kan också konfigureras med hjälp av **verktyg**  >  **data Lake**  >  **alternativ och inställningar**.

![Konfigurera en lokal datarotmapp](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Ett U-SQL-projekt krävs för en lokal körning. U-SQL-projektets arbets katalog används för den lokala U-SQL-körningens arbets katalog. Kompileringsfel, kör loggar och andra jobb körnings-relaterade filer skapas och lagras i mappen arbets katalog under den lokala körningen. Varje gång du kör skriptet rensas alla filer i arbets katalogen och återskapas.

## <a name="local-runs-with-a-local-project-account"></a>Lokala körningar med ett konto med lokalt projekt

Ett konto med **lokalt projekt** är ett projekt isolerat lokalt beräknings konto för varje projekt med en isolerad lokal datarotmapp. Varje aktivt U-SQL-projekt som öppnas i Solution Explorer i Visual Studio har ett motsvarande `(Local-project: <project name>)` konto. Kontona visas i både Server Explorer i Visual Studio och i U-SQL Script Editor-marginalen.  

Kontot för **lokalt projekt** ger en ren och isolerad utvecklings miljö. Ett **lokalt dator** konto har en delad lokal datarotmapp som lagrar metadata och indata och utdata för alla lokala jobb. Men ett konto med **lokalt projekt** skapar en tillfällig lokal datarotmapp i arbets katalogen u-SQL-projekt varje gång ett U-SQL-skript körs. Den här tillfälliga datarotmappen rensas när en återuppbyggnad eller omkörning sker. 

Ett U-SQL-projekt hanterar den isolerade lokala körnings miljön via en projekt referens och egenskap. Du kan konfigurera indata-data källor för U-SQL-skript i både projektet och i de databas miljöer som refereras till.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Hantera indata-datakälla för ett lokalt projekt konto 

Ett U-SQL-projekt skapar en lokal datarotmapp och konfigurerar data för ett **lokalt projekt** konto. En tillfällig datarotmapp rensas och återskapas under U-SQL-projektets arbets katalog varje gång en ombyggnad och lokal körning sker. Alla data källor som konfigureras av U-SQL-projektet kopieras till den här tillfälliga lokala datarotmappen innan det lokala jobbet körs. 

Du kan konfigurera rotmappen för dina data källor. Högerklicka på **U-SQL projekt**  >  **egenskap**  >  **test data källa**. När du kör ett U-SQL-skript på ett **lokalt projekt** konto kopieras alla filer och undermappar i mappen **test data källa** till den tillfälliga lokala datarotmappen. Filer i undermappar ingår. När ett lokalt jobb har körts kan du också hitta utdata i den tillfälliga lokala datarotmappen i projektets arbets katalog. Alla dessa utdata tas bort och rensas när projektet skapas och rensas. 

![Konfigurera ett projekts test data Källa](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Hantera en refererad databas miljö för ett **lokalt projekt** konto 

Om en U-SQL-fråga använder eller frågar med U-SQL Database-objekt, måste du göra databas miljöerna klara lokalt innan du kör U-SQL-skriptet lokalt. För ett **lokalt projekt** konto kan u-SQL Database-beroenden hanteras av u-SQL-projektets referenser. Du kan lägga till projekt referenser i u-SQL-databasen till ditt U-SQL-projekt. Innan du kör U-SQL-skript på ett **lokalt projekt** konto distribueras alla refererade databaser till den tillfälliga lokala datarotmappen. Och för varje körning rensas den tillfälliga datarotmappen som en ny isolerad miljö.

Se den här relaterade artikeln:
* Lär dig hur du hanterar definitioner av U-SQL-databaser och referenser i [u-SQL Database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Skillnaden mellan **lokala datorer** och konton med **lokalt projekt**

Ett **lokalt dator** konto simulerar ett Azure Data Lake Analytics-konto på användarnas lokala datorer. Den delar samma upplevelse med ett Azure Data Lake Analytics-konto. Ett konto med **lokalt projekt** ger en användarvänlig lokal utvecklings miljö. Den här miljön hjälper användare att distribuera databas referenser och indata innan de kör skript lokalt. Ett **lokalt dator** konto ger en delad permanent miljö som kan nås via alla projekt. Ett konto med **lokalt projekt** ger en isolerad utvecklings miljö för varje projekt. Den uppdateras för varje körning. Ett konto med **lokalt projekt** ger en snabbare utvecklings upplevelse genom att snabbt använda nya ändringar.

Mer skillnader mellan **lokala datorer** och konton med **lokalt projekt** visas i följande tabell:

|Skillnads vinkel|Lokal dator|Lokalt projekt|
|----------------|---------------|---------------|
|Lokal åtkomst|Kan nås av alla projekt.|Endast motsvarande projekt har åtkomst till det här kontot.|
|Lokal rotmapp|En permanent lokal mapp. Konfigureras med hjälp av **verktyg**  >  **data Lake**  >  **alternativ och inställningar**.|En tillfällig mapp som skapats för varje lokal körning under arbets katalogen U-SQL-projekt. Mappen rensas när en återuppbyggnad eller omkörning sker.|
|Indata för ett U-SQL-skript|Den relativa sökvägen under den permanenta lokala data rot katalogen.|Ange till test data källa för **U-SQL-projektets projekt egenskap**  >  . Alla filer och undermappar kopieras till den tillfälliga datarotmappen innan en lokal körning.|
|Utdata för ett U-SQL-skript|Relativ sökväg under den permanenta lokala data rot katalogen.|Utdata till den tillfälliga datarotmappen. Resultaten rensas när en återuppbyggnad eller omkörning sker.|
|Refererad databas distribution|Refererade databaser distribueras inte automatiskt när de körs mot ett **lokalt dator** konto. Det är detsamma för att skicka till ett Azure Data Lake Analytics-konto.|Refererade databaser distribueras till det **lokala projekt** kontot automatiskt före en lokal körning. Alla databas miljöer rensas och omdistribueras när en återuppbyggnad eller omkörning sker.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>En lokal körning med U-SQL SDK

Du kan köra U-SQL-skript lokalt i Visual Studio och även använda Azure Data Lake U-SQL SDK för att köra U-SQL-skript lokalt med kommando rads-och programmerings gränssnitt. Genom de här gränssnitten kan du automatisera U-SQL lokala körningar och tester.

Läs mer om [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Så här testar du din Azure Data Lake Analyticss kod](data-lake-analytics-cicd-test.md).
