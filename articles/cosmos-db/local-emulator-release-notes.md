---
title: Information om hämtning och version av Azure Cosmos DB-emulator
description: Hämta versions anteckningar för Azure Cosmos DB emulator för olika versioner och information om hämtning.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 92fdef48556a3306ee5e3f10e07ff609a8fc4be4
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656231"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB emulator – viktig information och information om hämtning
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Den här artikeln visar viktig information om Azure Cosmos DB emulator med en lista över funktions uppdateringar som har gjorts i varje version. Den visar även den senaste versionen av emulatorn för att hämta och använda.

## <a name="download"></a>Ladda ned

| | |
|---------|---------|
|**Ladda ned MSI**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Komma igång**|[Utveckla lokalt med Azure Cosmos DB emulator](local-emulator.md)|

## <a name="release-notes"></a>Viktig information

### <a name="2119-3-december-2020"></a>2.11.9 (3 december 2020)

 - I den här versionen åtgärdas några problem med Azure Cosmos DB emulator-funktionen förutom den allmänna innehålls uppdateringen som återspeglar de senaste funktionerna och förbättringarna i Azure Cosmos DB:
 * Åtgärda ett problem där stora dokument nytto Last förfrågningar inte fungerar när du använder direkt läge och Java-klientprogram.
 * Korrigering för ett anslutnings problem med MongoDB-slutpunkt version 3,6 när den är avsedd för .NET-baserade program.

### <a name="2118-6-november-2020"></a>2.11.8 (6 november 2020)

 - Den här versionen innehåller en uppdatering för Cosmos-emulatorn Datautforskaren och åtgärdar ett problem där TLS 1,3-klienter försöker öppna Datautforskaren.

### <a name="2116-6-october-2020"></a>2.11.6 (6 oktober 2020)

 - Den här versionen åtgärdar ett samtidigt relaterat problem när flera behållare kan skapas samtidigt. I sådana fall är emulatorns data kvar i ett skadat tillstånd och följande API-förfrågningar till emulatorns slut punkt kunde inte utföras, fel meddelande om att tjänsten inte är tillgänglig, vilket kräver en omstart och en återställning av emulatorns lokala data.

### <a name="2115-23-august-2020"></a>2.11.5 (23 augusti 2020)

Den här versionen lägger till två nya start alternativ för Cosmos-emulator: 

* "/EnablePreview" – den aktiverar förhands gransknings funktioner för emulatorn. För hands versions funktionerna som fortfarande är under utveckling och som kan nås via CI och exempel skrivning.
* "/EnableAadAuthentication" – det gör att emulatorn kan acceptera anpassade Azure Active Directory tokens som ett alternativ till primär nycklarna för Azure Cosmos. Den här funktionen är fortfarande under utveckling; Det finns för närvarande inte stöd för vissa roll tilldelningar och andra behörighets inställningar.

### <a name="2112-07-july-2020"></a>2.11.2 (07 juli 2020)

- Den här versionen ändrar hur ETL-spårningar krävs när fel sökning av Cosmos-emulatorn samlas in. WPR (Windows Performance runtime-verktyg) är nu standard verktygen för att fånga ETL-baserade spår medan tidigare LOGMAN-baserad infångning är inaktuell. Den här ändringen krävs delvis eftersom de senaste säkerhets uppdateringarna i Windows hade en oväntad inverkan på hur LOGMAN fungerar vid körning via Cosmos-emulatorn.

### <a name="2111-10-june-2020"></a>2.11.1 (10 juni 2020)

- I den här versionen åtgärdas några buggar som rör emulator Datautforskaren. I vissa fall när du använder emulatorn Datautforskaren via en webbläsare, går det inte att ansluta till Cosmos-emulatorns slut punkt och alla relaterade åtgärder som att skapa en databas eller en behållare resulterar i fel. Det andra problem som har åtgärd ATS är relaterat till att skapa ett objekt från en JSON-fil med Datautforskaren överförings åtgärd.

### <a name="2110"></a>2.11.0

- I den här versionen införs stöd för autoskalning av allokerat data flöde. De här nya funktionerna är möjligheten att ange en anpassad högsta insamlad data flödes nivå i enheter för programbegäran (RU/s), aktivera autoskalning på befintliga databaser och behållare, samt programmerings stöd via Azure Cosmos DB SDK: er.
- Åtgärda ett problem vid frågor till och med ett stort antal dokument (över 1 GB). emulatorn Miss känner till den interna fel status koden 500.

### <a name="292"></a>2.9.2

- Den här versionen åtgärdar ett fel vid aktivering av stöd för MongoDb-slutpunkt version 3,2. Den lägger också till stöd för att generera ETL-spårningar för fel söknings användning med WPR i stället för LOGMAN.

### <a name="291"></a>2.9.1

- I den här versionen åtgärdas några problem i API-stöd för frågor och en återställning av kompatibilitet med äldre OSs som Windows Server 2012.

### <a name="290"></a>2.9.0

- Den här versionen lägger till alternativet för att ställa in konsekvens till konsekvent prefix och öka Max gränsen för användare och behörigheter.

### <a name="272"></a>2.7.2

- Den här versionen lägger till MongoDB version 3,6-Server stöd för Cosmos-emulatorn. Starta en MongoDB-slutpunkt som är mål version 3,6 av tjänsten genom att starta emulatorn från en administratörs kommando rad med alternativet "/EnableMongoDBEndpoint = 3.6".

### <a name="270"></a>2.7.0

- Den här versionen åtgärdar en regression, som hindrade användare från att köra frågor mot SQL API-kontot från emulatorn vid användning av .NET Core-eller x86 .NET-baserade klienter.

### <a name="246"></a>2.4.6

- Den här versionen ger paritet med funktionerna i Azure Cosmos-tjänsten från och med den 2019 juli, med undantag som anges i [utveckla lokalt med Azure Cosmos DB emulator](local-emulator.md). Den åtgärdar också flera buggar som är relaterade till emulatorn stängs av när de anropas via kommando raden och interna IP-adresser åsidosätter för SDK-klienter som använder anslutning med direkt läge.

### <a name="243"></a>2.4.3

- Inaktive rad startar MongoDB-tjänsten som standard. Endast SQL-slutpunkten är aktive rad som standard. Användaren måste starta slut punkten manuellt med hjälp av kommando rads alternativet "/EnableMongoDbEndpoint" i emulatorn. Nu är det som alla andra tjänst slut punkter, till exempel Gremlin, Cassandra och Table.
- En bugg har åtgärd ATS i emulatorn vid start med "/AllowNetworkAccess" där Gremlin, Cassandra och table-slutpunkterna inte korrekt hanterade begär Anden från externa klienter.
- Lägg till direkta anslutnings portar i brand Väggs reglernas inställningar.

### <a name="240"></a>2.4.0

- Ett problem har åtgärd ATS med att emulatorn inte kan starta när appar för nätverks övervakning, till exempel Pulse client, finns på värddatorn.
