---
title: Flytta data mellan utskalade molndatabaser
description: 'Förklarar hur du hanterar Shards och flyttar data via en lokal tjänst med hjälp av Elastic Database-API: er.'
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793474"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Flytta data mellan utskalade molndatabaser
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Om du är en program vara som tjänst utvecklare och plötsligt din app är fantastisk efter frågan, måste du anpassa tillväxten. Lägg till fler databaser (Shards). Hur distribuerar du om data till nya databaser utan att störa data integriteten? Använd **verktyget Dela och slå samman** för att flytta data från begränsade databaser till nya databaser.  

Verktyget Dela och slå samman körs som en Azure-webbtjänst. En administratör eller utvecklare använder verktyget för att flytta shardletar (data från en Shard) mellan olika databaser (Shards). Verktyget använder Shard Map-hantering för att underhålla databasens metadata och säkerställa konsekventa mappningar.

![Översikt][1]

## <a name="download"></a>Ladda ned

[Microsoft. Azure. SqlDatabase. ElasticScale. service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentation

1. [Själv studie kurs om Split-sammanslagnings verktyg i Elastic Database](elastic-scale-configure-deploy-split-and-merge.md)
2. [Säkerhets konfiguration för delad sammanslagning](elastic-scale-split-merge-security-configuration.md)
3. [Säkerhets överväganden för delad sammanslagning](elastic-scale-split-merge-security-configuration.md)
4. [Karthantering för shard](elastic-scale-shard-map-management.md)
5. [Migrera befintliga databaser för att skala ut](elastic-convert-to-use-elastic-tools.md)
6. [Elastic Database-verktyg](elastic-scale-introduction.md)
7. [Ordlista för verktyg i elastiska databaser](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Varför ska jag använda verktyget Dela/slå samman

- **Enkelt**

  Programmen måste sträckas ut flexibelt över gränserna för en enkel databas i Azure SQL Database. Använd verktyget för att flytta data efter behov till nya databaser och behålla integriteten.

- **Dela till tillväxt**

  Om du vill öka den övergripande kapaciteten för att hantera explosiv tillväxt skapar du ytterligare kapacitet genom att horisontell partitionering data och genom att distribuera dem i stegvisa fler databaser tills kapacitets behoven är uppfyllda. Detta är ett primtal exempel på **delnings** funktionen.

- **Sammanfoga till Krymp**

  Kapaciteten behöver krympa på grund av säsongs typen för ett företag. Med verktyget kan du skala ned till färre skalnings enheter när verksamheten är långsam. Funktionen merge i den elastiska skalnings Split-Merge tjänsten täcker detta krav.

- **Hantera hotspots genom att flytta shardletar**

  Med flera klienter per databas kan fördelningen av shardletar till Shards leda till kapacitets Flask halsar på vissa Shards. Detta kräver omtilldelning av shardletar eller att flytta upptagen shardletar till nya eller mindre använda Shards.

## <a name="concepts--key-features"></a>Begrepp & viktiga funktioner

- **Kund värd tjänster**

  Delnings sammanslagningen levereras som en kund värd tjänst. Du måste distribuera och vara värd för tjänsten i Microsoft Azure prenumerationen. Paketet som du hämtar från NuGet innehåller en konfigurations mal len som du kan använda för att slutföra informationen för din aktuella distribution. Mer information finns i [självstudierna dela och slå samman](elastic-scale-configure-deploy-split-and-merge.md) . Eftersom tjänsten körs i din Azure-prenumeration kan du kontrol lera och konfigurera de flesta säkerhets aspekter av tjänsten. Standard mal len innehåller alternativ för att konfigurera TLS, certifikatbaserad klientautentisering, kryptering för lagrade autentiseringsuppgifter, DoS-skydd och IP-begränsningar. Du hittar mer information om säkerhets aspekterna i följande dokument [delnings-och säkerhets konfiguration för delad sammanslagning](elastic-scale-split-merge-security-configuration.md).

  Den distribuerade standard tjänsten körs med en anställd och en webb roll. Varje använder storleken på den virtuella a1-datorn i Azure Cloud Services. Du kan inte ändra de här inställningarna när du distribuerar paketet, men du kan ändra dem efter en lyckad distribution i moln tjänsten som körs (via Azure Portal). Observera att arbets rollen inte får konfigureras för mer än en enskild instans av tekniska skäl.

- **Integrering av Shard-karta**

  Tjänsten för delad sammanslagning interagerar med Shard-kartan för programmet. När du använder tjänsten för delad sammanslagning för att dela eller slå samman intervall eller för att flytta shardletar mellan Shards, behåller tjänsten automatiskt Shard-mappningen uppdaterad. För att göra det ansluter tjänsten till Shard Map Manager-databasen i programmet och behåller intervall och mappningar som förlopp för delning/sammanfogning/flyttning. Detta säkerställer att Shard-kartan alltid presenterar en uppdaterad vy när delade sammanslagnings åtgärder pågår. Åtgärder för att dela, slå samman och shardlet rörelse implementeras genom att flytta en batch med shardletar från källan Shard till mål-Shard. Under shardlet rörelse åtgärd markeras shardletar som omfattas av den aktuella batchen som offline i Shard-mappningen och är inte tillgängliga för data beroende routnings anslutningar med hjälp av **OpenConnectionForKey** -API: et.

- **Konsekventa shardlet-anslutningar**

  När data förflyttningen påbörjas för en ny batch med shardletar, stoppas alla Shard-mappningar som är beroende av data beroende och efterföljande anslutningar från API: erna för Shard-kartor till shardletar blockeras medan data flytten pågår för att undvika inkonsekvenser. Anslutningar till andra shardletar på samma Shard kommer också att stoppas, men kommer att lyckas igen omedelbart vid nya försök. När gruppen har flyttats markeras shardletar igen för mål Shard och källdata tas bort från källan Shard. Tjänsten genomgår de här stegen för varje batch tills alla shardletar har flyttats. Detta leder till flera stopp åtgärder för anslutningar under hela åtgärden dela/slå samman/flytta.  

- **Hantera shardlet-tillgänglighet**

  Att begränsa anslutningen till den aktuella batchen av shardletar enligt beskrivningen ovan begränsar omfånget till en batch med shardletar i taget. Detta föredras över en metod där hela Shard skulle vara offline för alla dess shardletar under en split-eller sammanslagnings åtgärd. En grupps storlek, definierad som antalet distinkta shardletar som ska flyttas i taget, är en konfigurations parameter. Den kan definieras för varje delnings-och sammanslagnings åtgärd beroende på programmets tillgänglighets-och prestanda behov. Observera att intervallet som låses i Shard-kartan kan vara större än den angivna batchstorleken. Detta beror på att tjänsten väljer intervall storlek, så att det faktiska antalet horisontell partitionering-nyckel värden i data ungefär matchar batchstorleken. Detta är viktigt att komma ihåg särskilt för glest fyllda horisontell partitionering-nycklar.

- **Metadata-lagring**

  Tjänsten för delad sammanslagning använder en databas för att underhålla dess status och för att spara loggar under bearbetning av begär Anden. Användaren skapar den här databasen i sin prenumeration och tillhandahåller anslutnings strängen för den i konfigurations filen för tjänst distributionen. Administratörer från användarens organisation kan också ansluta till den här databasen för att granska begär ande förloppet och undersöka detaljerad information om potentiella problem.

- **Horisontell partitionering – medvetenhet**

  Tjänsten för delad sammanslagning skiljer sig mellan (1) shardade-tabeller, (2) referens tabeller och (3) normala tabeller. Semantiken för en delnings-/sammanfognings-/flyttnings åtgärd beror på vilken typ av tabell som används och definieras enligt följande:

  - **Shardade-tabeller**

    Delnings-, sammanfognings-och flyttnings åtgärder flyttar shardletar från källa till mål Shard. När den övergripande begäran har slutförts är dessa shardletar inte längre tillgängliga på källan. Observera att mål tabellerna måste finnas på mål-Shard och får inte innehålla data i mål intervallet före bearbetningen av åtgärden.

  - **Referens tabeller**

    För referens tabeller kopierar åtgärderna dela, sammanfoga och flytta data från källan till mål-Shard. Observera dock att inga ändringar sker på mål-Shard för en specifik tabell om det redan finns en rad i den här tabellen på målet. Tabellen måste vara tom för att det ska gå att bearbeta en referens tabell kopierings åtgärd.

  - **Andra tabeller**

    Andra tabeller kan finnas antingen på källan eller målet för en delnings-och sammanslagnings åtgärd. Tjänsten för delad sammanslagning ignorerar de här tabellerna för data förflyttning eller kopierings åtgärder. Observera dock att de kan störa dessa åtgärder i händelse av begränsningar.

    Informationen om referens vs. shardade-tabeller tillhandahålls av `SchemaInfo` API: erna på Shard-kartan. Följande exempel illustrerar användningen av dessa API: er på ett angivet Shard Map Manager-objekt:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabellerna region och nation definieras som referens tabeller och kopieras med åtgärder för att dela/sammanfoga/flytta. "kund" och "order" i sin tur definieras som shardade-tabeller. `C_CUSTKEY` och `O_CUSTKEY` fungerar som horisontell partitionering-nyckel.

- **Referens integritet**

  Tjänsten för delad sammanslagning analyserar beroenden mellan tabeller och använder sekundär nyckel-Primary Key-relationer för att mellanlagra åtgärder för att flytta referens tabeller och shardletar. I allmänhet kopieras referens tabeller först i beroende ordning och sedan kopieras shardletar i varje grupps beroende ordning. Detta är nödvändigt så att sekundär-och Shard-begränsningar på mål-respekteras när nya data kommer in.

- **Shard Map-konsekvens och eventuell slut för ande**

  I närvaro av problem återupptar tjänsten för delad sammanslagning åtgärder efter avbrott och syfte att slutföra pågående begär Anden. Det kan dock finnas återställnings bara situationer, t. ex. När mål-Shard tappas bort eller komprometteras utanför reparationen. Under dessa omständigheter kan vissa shardletar som skulle flyttas fortsätta att finnas på käll-Shard. Tjänsten säkerställer att shardlet-mappningar bara uppdateras när nödvändiga data har kopierats till målet. Shardletar tas bara bort från källan när alla data har kopierats till målet och motsvarande mappningar har uppdaterats. Borttagnings åtgärden sker i bakgrunden medan intervallet redan är online på mål-Shard. Tjänsten för delad sammanslagning garanterar alltid att mappningar som lagras i Shard-kartan är korrekta.

## <a name="the-split-merge-user-interface"></a>Användar gränssnittet för delad sammanslagning

Det delade sammanslagnings tjänst paketet innehåller en arbets roll och en webb roll. Webb rollen används för att skicka förfrågningar om att dela kopplingar på ett interaktivt sätt. Huvud komponenterna i användar gränssnittet är följande:

- **Åtgärdstyp**

  Åtgärds typen är en alternativ knapp som styr vilken typ av åtgärd som utförs av tjänsten för den här begäran. Du kan välja mellan scenarierna dela, slå samman och flytta. Du kan också avbryta en tidigare skickad åtgärd. Du kan använda delnings-, sammanfognings-och flyttnings begär Anden för intervall Shard Maps. List Shard Maps stöder bara flytt åtgärder.

- **Shard-karta**

  Nästa avsnitt i parametrarna för begäran täcker information om Shard-mappningen och databasen som är värd för din Shard-karta. I synnerhet måste du ange namnet på servern och databasen som är värd för shardmap, autentiseringsuppgifter för att ansluta till Shard Map-databasen och slutligen namnet på Shard-kartan. För närvarande accepterar åtgärden endast en enda uppsättning autentiseringsuppgifter. Autentiseringsuppgifterna måste ha tillräcklig behörighet för att utföra ändringar i Shard-mappningen samt för användar data på Shards.

- **Käll intervall (dela och slå samman)**

  En delnings-och sammanfognings åtgärd bearbetar ett intervall med hjälp av dess låga och höga nyckel. Om du vill ange en åtgärd med ett obundet högt nyckel värde markerar du kryss rutan "hög nyckel är max" och lämnar fältet för hög nyckel tomt. De intervall nyckel värden som du anger behöver inte exakt matcha en mappning och dess gränser i Shard-kartan. Om du inte anger några intervall gränser på alla tjänster kommer du att härleda det närmaste intervallet åt dig automatiskt. Du kan använda GetMappings.ps1 PowerShell-skriptet för att hämta de aktuella mappningarna i en specifik Shard-karta.

- **Beteende för delad källa (dela)**

  För delnings åtgärder definierar du punkten för att dela käll intervallet. Du gör detta genom att ange horisontell partitionering-nyckeln där du vill att delningen ska ske. Använd alternativ knappen för att ange om du vill att den nedre delen av intervallet (exklusive delnings nyckeln) ska flyttas, eller om du vill att den övre delen ska flyttas (inklusive delnings nyckeln).

- **Shardlet för källa (flytta)**

  Flyttnings åtgärder skiljer sig från delnings-eller sammanslagnings åtgärder eftersom de inte kräver ett intervall för att beskriva källan. En källa för flytt identifieras helt enkelt med det horisontell partitionering-nyckel värde som du planerar att flytta.

- **Mål Shard (dela)**

  När du har angett informationen om källan till delnings åtgärden, måste du definiera var du vill att data ska kopieras till genom att ange server-och databas namnet för målet.

- **Mål intervall (sammanslagning)**

  Sammanfoga åtgärder flytta shardletar till en befintlig Shard. Du kan identifiera den befintliga Shard genom att ange intervall gränserna för det befintliga intervallet som du vill sammanfoga med.

- **Batchstorlek**

  Batchstorleken styr antalet shardletar som försätts i offlineläge vid en tidpunkt under data flytten. Detta är ett heltals värde där du kan använda mindre värden när du är känslig för långa tids perioder för shardletar. Större värden ökar den tid som en specifik shardlet är offline men kan förbättra prestandan.

- **Åtgärds-ID (Avbryt)**

  Om du har en pågående åtgärd som inte längre behövs kan du avbryta åtgärden genom att ange dess åtgärds-ID i det här fältet. Du kan hämta åtgärds-ID: t från tabellen status för begäran (se avsnitt 8,1) eller från utdata i webbläsaren där du skickade begäran.

## <a name="requirements-and-limitations"></a>Krav och begränsningar

Den aktuella implementeringen av tjänsten för delad sammanslagning omfattas av följande krav och begränsningar:

- Shards måste finnas och registreras i Shard-kartan innan en delnings sammanslagnings åtgärd på dessa Shards kan utföras.
- Tjänsten skapar inte tabeller eller andra databas objekt automatiskt som en del av dess åtgärder. Det innebär att schemat för alla shardade-tabeller och referens tabeller måste finnas på mål-Shard före alla åtgärder för att dela/slå samman/flytta. Shardade-tabeller måste särskilt vara tomma i intervallet där nya shardletar ska läggas till med åtgärden dela/sammanfoga/flytta. Annars Miss söker åtgärden den inledande konsekvens kontrollen på mål-Shard. Observera också att referens data bara kopieras om referens tabellen är tom och att det inte finns några konsekvens garantier avseende andra samtidiga Skriv åtgärder i referens tabellerna. Vi rekommenderar detta: när du kör delnings-/sammanfognings åtgärder gör inga andra Skriv åtgärder ändringar i referens tabellerna.
- Tjänsten förlitar sig på rad identitet som upprättats av ett unikt index eller en nyckel som innehåller nyckeln horisontell partitionering för att förbättra prestanda och tillförlitlighet för stora shardletar. Detta gör att tjänsten kan flytta data till en ännu bättre granularitet än bara värdet för horisontell partitionering. Detta bidrar till att minska den maximala mängden logg utrymme och lås som krävs under åtgärden. Överväg att skapa ett unikt index eller en primär nyckel inklusive horisontell partitionering-nyckeln i en specifik tabell om du vill använda tabellen med delnings-/sammanfognings-/flyttnings begär Anden. Av prestanda skäl ska horisontell partitionering-nyckeln vara den inledande kolumnen i nyckeln eller indexet.
- Under bearbetningen av begär Anden kan vissa shardlet-data finnas både på käll-och mål-Shard. Detta är nödvändigt för att skydda mot haverier under shardlet-flyttningen. Integreringen av Split-Merge med Shard-kartan säkerställer att anslutningar via API: er för data beroende routning med metoden **OpenConnectionForKey** på Shard-kartan inte ser några inkonsekventa mellanliggande tillstånd. Men när du ansluter till käll-eller mål-Shards utan att använda **OpenConnectionForKey** -metoden kan inkonsekventa mellanliggande tillstånd visas när begär Anden om att dela/slå samman/flytta används. De här anslutningarna kan visa delvis eller duplicerade resultat beroende på tids inställningen eller Shard underliggande anslutningen. Den här begränsningen omfattar för närvarande de anslutningar som görs av elastisk skalning multi-Shard-frågor.
- Metadata-databasen för tjänsten för delad sammanslagning får inte delas mellan olika roller. En roll i den delade sammanslagnings tjänsten som körs vid mellanlagring måste till exempel peka på en annan metadata-databas än produktions rollen.

## <a name="billing"></a>Fakturering

Tjänsten för delad sammanslagning körs som en moln tjänst i din Microsoft Azure prenumeration. Avgifter för moln tjänster gäller därför för din instans av tjänsten. Om du inte ofta utför åtgärderna dela/sammanfoga/flytta rekommenderar vi att du tar bort din moln tjänst för delad sammanslagning. Det sparar kostnader för att köra eller distribuerade moln tjänst instanser. Du kan distribuera om och starta din körbara konfiguration när du behöver utföra delnings-eller sammanslagnings åtgärder.

## <a name="monitoring"></a>Övervakning

### <a name="status-tables"></a>Status tabeller

Tjänsten för delad sammanslagning innehåller tabellen **RequestStatus** i databasen för metadatalagret för övervakning av slutförda och pågående begär Anden. Tabellen innehåller en rad för varje begäran om delad sammanslagning som har skickats till den här instansen av tjänsten för delad sammanslagning. Den innehåller följande information för varje begäran:

- **Tidsstämpel**

  Tid och datum då begäran startades.

- **OperationId**

  Ett GUID som unikt identifierar begäran. Den här begäran kan också användas för att avbryta åtgärden medan den fortfarande pågår.

- **Status**

  Aktuell status för begäran. För pågående begär Anden visas även den aktuella fasen i vilken begäran är.

- **CancelRequest**

  En flagga som anger om begäran har avbrutits.

- **Förlopp**

  En procentuell uppskattning av slut för ande för åtgärden. Värdet 50 anger att åtgärden är cirka 50% slutfört.

- **Information**

  Ett XML-värde som innehåller en mer detaljerad förlopps rapport. Förlopps rapporten uppdateras regelbundet när rad uppsättningar kopieras från källa till mål. I händelse av fel eller undantag innehåller den här kolumnen också mer detaljerad information om felet.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Tjänsten för delad sammanslagning använder Azure-diagnostik som baseras på Azure SDK 2,5 för övervakning och diagnostik. Du styr konfigurationen för diagnostik enligt beskrivningen här: [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). Hämtnings paketet innehåller två diagnostiska konfigurationer – en för webb rollen och en för arbets rollen. Den innehåller definitionerna för att logga prestanda räknare, IIS-loggar, Windows-händelseloggen och händelse loggar för att dela sammanslagna program.

## <a name="deploy-diagnostics"></a>Distribuera diagnostik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill aktivera övervakning och diagnostik med diagnostisk konfiguration för webb-och arbets roller som tillhandahålls av NuGet-paketet kör du följande kommandon med hjälp av Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Du hittar mer information om hur du konfigurerar och distribuerar diagnostikinställningar här: [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Hämta diagnostik

Du kan enkelt komma åt diagnostiken från Visual Studio-Server Explorer i Azure-delen av Server Explorers trädet. Öppna en Visual Studio-instans och klicka på Visa i meny raden och Server Explorer. Klicka på Azure-ikonen för att ansluta till din Azure-prenumeration. Gå sedan till Azure-> lagrings-> `<your storage account>` ->-tabeller – > WADLogsTable. Mer information finns i [Server Explorer](/previous-versions/x603htbk(v=vs.140)).

![WADLogsTable][2]

WADLogsTable som marker ATS i bilden ovan innehåller detaljerade händelser från program loggen för den delade sammanslagnings tjänsten. Observera att standard konfigurationen av det hämtade paketet är riktad mot en produktions distribution. Därför är intervallet för loggar och räknare som hämtas från tjänst instanserna stora (5 minuter). För testning och utveckling sänker du intervallet genom att justera diagnostikinställningar för webbplatsens eller arbets rollens inställningar efter behov. Högerklicka på rollen i Visual Studio-Server Explorer (se ovan) och justera sedan överförings perioden i dialog rutan för konfigurations inställningarna för diagnostik:

![Konfiguration][3]

## <a name="performance"></a>Prestanda

I allmänhet är bättre prestanda förväntas från högre, mer utförda tjänst nivåer. Högre IO-, processor-och minnes tilldelningar för högre tjänst nivåer förbrukar Mass kopierings-och borttagnings åtgärder som används i tjänsten för delad sammanslagning. Av den anledningen ökar du tjänst nivån precis för dessa databaser under en angiven begränsad tids period.

Tjänsten utför även verifierings frågor som en del av dess normala åtgärder. De här verifierings frågorna söker efter oväntade förekomster av data i mål intervallet och ser till att alla åtgärder för att dela/sammanfoga/flytta startar från ett konsekvent tillstånd. Dessa frågor omfattar alla horisontell partitionering nyckel intervall som definieras av åtgärdens omfattning och batchstorleken som anges som en del av definitionen av begäran. Dessa frågor fungerar bäst när det finns ett index som har horisontell partitionering-nyckeln som den inledande kolumnen.

Dessutom tillåter en unikhets egenskap med horisontell partitionering-nyckeln som den inledande kolumnen att tjänsten använder en optimerad metod som begränsar resurs förbrukningen i termer av logg utrymme och minne. Den här egenskapen unikhet krävs för att flytta stora data storlekar (vanligt vis över 1 GB).

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Följ stegen i [distribuera en tjänst för delad sammanslagning](elastic-scale-configure-deploy-split-and-merge.md).
2. Ändra din moln tjänst konfigurations fil för den delade sammanslagnings distributionen så att den återspeglar de nya konfigurations parametrarna. En ny obligatorisk parameter är information om certifikatet som används för kryptering. Ett enkelt sätt att göra detta är att jämföra den nya konfigurations mal len från nedladdningen mot den befintliga konfigurationen. Se till att du lägger till inställningarna för "DataEncryptionPrimaryCertificateThumbprint" och "DataEncryptionPrimary" för både webb-och arbets rollen.
3. Innan du distribuerar uppdateringen till Azure måste du kontrol lera att alla aktiviteter som körs har delats för tillfället har avslut ATS. Du kan enkelt göra detta genom att fråga RequestStatus-och PendingWorkflows-tabellerna i databasen för den delade sammanslagningen av metadata för pågående begär Anden.
4. Uppdatera din befintliga moln tjänst distribution för delad sammanslagning i din Azure-prenumeration med det nya paketet och den uppdaterade tjänst konfigurations filen.

Du behöver inte tillhandahålla en ny metadata-databas för att dela upp och uppgradera. Den nya versionen kommer automatiskt att uppgradera den befintliga metadata-databasen till den nya versionen.

## <a name="best-practices--troubleshooting"></a>Metodtips och felsökning

- Definiera en test klient och utöva de viktigaste åtgärderna för att dela/slå samman/flytta med test klienten över flera Shards. Se till att alla metadata är korrekt definierade i Shard-kartan och att åtgärderna inte bryter mot begränsningar eller sekundär nycklar.
- Behåll klientens data storlek ovanför den maximala data storleken för den största klienten för att se till att du inte stöter på problem med data storlek. Detta hjälper dig att utvärdera en övre gräns på den tid det tar att flytta en enskild klient organisation.
- Se till att schemat tillåter borttagningar. Tjänsten för delad sammanslagning kräver möjlighet att ta bort data från käll-Shard när data har kopierats till målet. **Ta bort utlösare** kan till exempel förhindra att tjänsten tar bort data på källan och kan leda till att åtgärder Miss lyckas.
- Horisontell partitionering-nyckeln bör vara den inledande kolumnen i primär nyckeln eller en unik index definition. Det säkerställer bästa prestanda för delnings-eller sammanfognings frågorna och för den faktiska data flytten och de borttagnings åtgärder som alltid fungerar i horisontell partitionering nyckel intervall.
- Samordna tjänsten för delnings sammanslagning i regionen och data centret där databaserna finns.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png