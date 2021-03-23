---
title: Data kvarhållning och lagring i Azure Application Insights | Microsoft Docs
description: Policy för kvarhållning och sekretess policy
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6a7545d4d927418e6153ce3bf439fc8c3e2dc702
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774923"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datainsamling, kvarhållning och lagring i Application Insights

När du installerar [Azure Application Insights][start] SDK i appen skickas telemetri om din app till molnet. Välbekanta utvecklare vill veta exakt vilka data som skickas, vad som händer med data och hur de kan behålla kontrollen över dem. I synnerhet kan känsliga data skickas, där lagras det och hur säkert är det? 

Först, det korta svaret:

* Standardmodulerna för telemetri som körs "out of box" är osannolika att skicka känsliga data till tjänsten. Telemetrin är bekymrad med belastnings-, prestanda-och användnings statistik, undantags rapporter och andra diagnostikdata. De viktigaste användar data som visas i diagnostiska rapporter är URL: er. men appen bör inte i något fall lagra känsliga data i oformaterad text i en URL.
* Du kan skriva kod som skickar ytterligare anpassad telemetri som hjälper dig med diagnostik-och övervaknings användningen. (Den här utökningen är en fantastisk funktion i Application Insights.) Det skulle vara möjligt att av misstag skriva den här koden så att den innehåller personliga och andra känsliga data. Om programmet fungerar med sådana data bör du använda en grundlig gransknings process för all kod du skriver.
* När du utvecklar och testar din app är det enkelt att kontrol lera vad som skickas av SDK: n. Data visas i fönstret för fel sökning av utdata i IDE-och webbläsare.
* Du kan välja plats när du skapar en ny Application Insights-resurs. Lär dig mer om Application Insights tillgänglighet per region [här](https://azure.microsoft.com/global-infrastructure/services/?products=all).
*   Granska insamlade data, eftersom detta kan innehålla data som tillåts i vissa fall, men inte andra.  Ett lämpligt exempel på detta är enhets namnet. Enhets namnet från en server har ingen sekretess påverkan och är användbart, men ett enhets namn från en telefon eller bärbar dator kan ha en sekretess påverkan och vara mindre användbar. Ett SDK som främst utvecklats för mål servrar, skulle samla in enhets namn som standard och det kan behöva skrivas över i både normala händelser och undantag.

Resten av den här artikeln är mer utförligare på svaren. Den är utformad för att vara fristående, så att du kan visa den för kollegor som inte ingår i ditt omedelbara team.

## <a name="what-is-application-insights"></a>Vad är Application Insights?
[Azure Application Insights][start] är en tjänst som tillhandahålls av Microsoft och som hjälper dig att förbättra prestanda och användbarhet för ditt Live-program. Den övervakar ditt program hela tiden den körs, både under testningen och när du har publicerat eller distribuerat det. Application Insights skapar diagram och tabeller som visar dig, till exempel vilka tider på dagen du får de flesta användare, hur appen är och hur väl den betjänas av externa tjänster som den är beroende av. Om det uppstår krascher, fel eller prestanda problem kan du söka igenom telemetridata i detalj för att diagnosticera orsaken. Tjänsten kommer att skicka e-postmeddelanden till dig om det finns några ändringar i appens tillgänglighet och prestanda.

För att få den här funktionen installerar du en Application Insights SDK i ditt program, som blir en del av koden. När din app körs övervakar SDK: n sin åtgärd och skickar telemetri till Application Insightss tjänsten. Det här är en moln tjänst som körs av [Microsoft Azure](https://azure.com). (Men Application Insights fungerar för alla program, inte bara program som finns i Azure.)

Tjänsten Application Insights lagrar och analyserar Telemetrin. Om du vill se analysen eller söka igenom den lagrade Telemetrin loggar du in på ditt Azure-konto och öppnar Application Insights resursen för ditt program. Du kan också dela åtkomst till data med andra medlemmar i din grupp eller med angivna Azure-prenumeranter.

Du kan ha exporterade data från tjänsten Application Insights, till exempel till en databas eller till externa verktyg. Du anger varje verktyg med en särskild nyckel som du får från tjänsten. Nyckeln kan återkallas vid behov. 

Application Insights SDK: er är tillgängliga för en mängd olika program typer: webb tjänster som finns i dina egna Java-eller ASP.NET-servrar eller i Azure. webb klienter – det vill säga koden som körs på en webb sida. skrivbordsappar och tjänster, enhets program som Windows Phone, iOS och Android. Alla skickar telemetri till samma tjänst.

## <a name="what-data-does-it-collect"></a>Vilka data samlas in?
Det finns tre data Källor:

* SDK, som du integrerar med din app, antingen [i utvecklings](./asp-net.md) -eller [körnings tid](./monitor-performance-live-website-now.md). Det finns olika SDK: er för olika program typer. Det finns också en [SDK för webb sidor](./javascript.md)som läses in i slutanvändarens webbläsare tillsammans med sidan.
  
  * Varje SDK har ett antal [moduler](./configuration-with-applicationinsights-config.md), som använder olika tekniker för att samla in olika typer av telemetri.
  * Om du installerar SDK i utvecklingen kan du använda dess API för att skicka din egen telemetri, förutom standardmodulerna. Den här anpassade Telemetrin kan innehålla alla data som du vill skicka.
* I vissa webb servrar finns det även agenter som körs tillsammans med appen och skicka telemetri om processor, minne och nätverks användning. Till exempel kan virtuella Azure-datorer, Docker-värdar och [Java-EE-servrar](./java-agent.md) ha sådana agenter.
* [Tillgänglighets test](./monitor-web-app-availability.md) är processer som körs av Microsoft som skickar begär anden till din webbapp med jämna mellanrum. Resultaten skickas till Application Insights tjänsten.

### <a name="what-kinds-of-data-are-collected"></a>Vilka typer av data samlas in?
Huvud kategorierna är:

* [Telemetri för webb server](./asp-net.md) – HTTP-begäranden.  URI, tids åtgång för att bearbeta begäran, svarskod, klientens IP-adress. `Session id`.
* [Webb sidor](./javascript.md) – antal sidor, användare och sessioner. Sid inläsnings tider. Undantag. AJAX-anrop.
* Prestanda räknare – minne, CPU, i/o, nätverks användning.
* Klient-och Server kontext – operativ system, språk, enhets typ, webbläsare, skärmupplösning.
* [Undantag](./asp-net-exceptions.md) och krascher – **stack dum par**, `build id` , CPU-typ. 
* [Beroenden](./asp-net-dependencies.md) – anrop till externa tjänster som rest, SQL, Ajax. URI eller anslutnings sträng, varaktighet, lyckades, kommando.
* [Tillgänglighets test](./monitor-web-app-availability.md) – varaktighet för test och steg, svar.
* [Spåra loggar](./asp-net-trace-logs.md) och [anpassad telemetri](./api-custom-events-metrics.md)  -  **allt du kodar till dina loggar eller telemetri**.

[Mer information](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hur kan jag verifiera vad som samlas in?
Om du utvecklar appen med Visual Studio kör du appen i fel söknings läge (F5). Telemetrin visas i fönstret utdata. Därifrån kan du kopiera den och formatera den som JSON för enkel granskning. 

![Skärm bild som visar hur du kör appen i fel söknings läge i Visual Studio.](./media/data-retention-privacy/06-vs.png)

Det finns också en mer lättläst vy i fönstret diagnostik.

För webb sidor öppnar du webbläsarens fel söknings fönster.

![Tryck på F12 och öppna fliken nätverk.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan jag skriva kod för att filtrera Telemetrin innan den skickas?
Detta skulle vara möjligt genom att skriva ett [plugin-program för telemetri-processor](./api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Hur länge sparas data?
Rå data punkter (det vill säga objekt som du kan fråga i analyser och granska i sökningen) bevaras i upp till 730 dagar. Du kan [välja en Retentions tid](./pricing.md#change-the-data-retention-period) på 30, 60, 90, 120, 180, 270, 365, 550 eller 730 dagar. Om du behöver behålla data längre än 730 dagar kan du använda [kontinuerlig export](./export-telemetry.md) för att kopiera den till ett lagrings konto under data inmatningen. 

Data som hålls längre än 90 dagar debiteras tilläggs avgifterna. Läs mer om Application Insights priser på [sidan för Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/).

Sammanställda data (det vill säga antal, medelvärden och andra statistiska data som visas i Metric Explorer) behålls till en kornigt 1 minut i 90 dagar.

[Fel söknings ögonblicks bilder](./snapshot-debugger.md) lagras i 15 dagar. Den här bevarande principen anges per tillämpning. Om du behöver öka det här värdet kan du begära en ökning genom att öppna ett support ärende i Azure Portal.

## <a name="who-can-access-the-data"></a>Vem som kan komma åt data?
Informationen är synlig för dig och, om du har ett organisations konto, ditt team medlemmar. 

Den kan exporteras av dig och dina team medlemmar och kan kopieras till andra platser och skickas till andra personer.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Vad gör Microsoft med den information som min app skickar till Application Insights?
Microsoft använder endast data för att tillhandahålla tjänsten till dig.

## <a name="where-is-the-data-held"></a>Var lagras data?
* Du kan välja plats när du skapar en ny Application Insights-resurs. Lär dig mer om Application Insights tillgänglighet per region [här](https://azure.microsoft.com/global-infrastructure/services/?products=all).

## <a name="how-secure-is-my-data"></a>Hur säkert är mina data?
Application Insights är en Azure-tjänst. Säkerhets principer beskrivs i [Azure-säkerhet, sekretess och efterlevnad White Paper](https://go.microsoft.com/fwlink/?linkid=392408).

Data lagras i Microsoft Azure-servrar. För konton i Azure Portal beskrivs konto begränsningar i [dokumentet Azure säkerhet, sekretess och regelefterlevnad](https://go.microsoft.com/fwlink/?linkid=392408).

Åtkomst till dina data från Microsoft-personal är begränsad. Vi kommer endast åt dina data med din tillåtelse och om det är nödvändigt för att stödja din användning av Application Insights. 

Data som sammanställs över alla våra kunders program (till exempel data hastigheter och den genomsnittliga storleken på spåren) används för att förbättra Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Kan någon annans telemetri störa mina Application Insights data?
De kan skicka ytterligare telemetri till ditt konto genom att använda Instrumentation-nyckeln, som du hittar i koden för dina webb sidor. Med tillräckligt med ytterligare data, representerar dina mått inte korrekt din apps prestanda och användning.

Kom ihåg att ta bort Instrumentation-nyckeln om du delar kod med andra projekt.

## <a name="is-the-data-encrypted"></a>Är data krypterade?
Alla data krypteras i vila och flyttas mellan data Center.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Är data krypterade från mitt program till Application Insights servrar?
Ja, vi använder HTTPS för att skicka data till portalen från nästan alla SDK: er, inklusive webb servrar, enheter och HTTPS-webbsidor. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Skapar SDK temporär lokal lagring?

Ja, vissa telemetri-kanaler behåller data lokalt om det inte går att nå en slut punkt. Granska nedan för att se vilka ramverk och telemetri-kanaler som påverkas.

Telemetri kanaler som använder lokala lagrings platser för att skapa temporära filer i katalogen TEMP eller APPDATA, som är begränsade till det specifika konto som kör programmet. Detta kan inträffa när en slut punkt tillfälligt var otillgänglig eller om du träffar begränsnings gränsen. När problemet har lösts fortsätter telemetri-kanalen att skicka alla nya och sparade data.

Den här sparade informationen är inte krypterad lokalt. Om detta är ett problem, granskar du data och begränsar insamlingen av privata data. (Mer information finns i [Exportera och ta bort privata data](../logs/personal-data-mgmt.md#how-to-export-and-delete-private-data).)

Om en kund behöver konfigurera den här katalogen med specifika säkerhets krav, kan den konfigureras per ramverk. Kontrol lera att processen som kör ditt program har Skriv behörighet till den här katalogen, men kontrol lera också att den här katalogen är skyddad för att undvika att telemetri läses av oönskade användare.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` används för att spara data. Den här platsen kan inte konfigureras från konfigurations katalogen och behörigheterna för åtkomst till den här mappen är begränsade till den aktuella användaren med nödvändiga autentiseringsuppgifter. (Mer information finns i [implementering](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.NET

Som standard `ServerTelemetryChannel` använder den aktuella användarens lokala app data-mapp `%localAppData%\Microsoft\ApplicationInsights` eller Temp-mappen `%TMP%` . (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.)


Via konfigurations fil:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via kod:

- Ta bort ServerTelemetryChannel från konfigurations filen
- Lägg till det här kodfragmentet i konfigurationen:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Som standard `ServerTelemetryChannel` använder den aktuella användarens lokala app data-mapp `%localAppData%\Microsoft\ApplicationInsights` eller Temp-mappen `%TMP%` . (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.) 

I en Linux-miljö kommer lokal lagring att inaktive ras om inte en lagringsmapp anges.

> [!NOTE]
> Med version 2.15.0-beta3 och större lokal lagring skapas nu automatiskt för Linux, Mac och Windows. För icke-Windows-system skapar SDK: n automatiskt en lokal lagringsmapp baserat på följande logik:
> - `${TMPDIR}` – Om `${TMPDIR}` miljövariabeln anges används den här platsen.
> - `/var/tmp` – om den föregående platsen inte finns försöker vi `/var/tmp` .
> - `/tmp` – om båda de föregående platserna inte finns provar vi `tmp` . 
> - Om ingen av dessa platser finns lokalt lagras inte och manuell konfiguration krävs fortfarande. [För fullständig implementerings information](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

Följande kodfragment visar hur du ställer in `ServerTelemetryChannel.StorageFolder` i- `ConfigureServices()` metoden för `Startup.cs` klassen:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Mer information finns i [AspNetCore anpassad konfiguration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Som standard `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` används för att spara data. Behörigheter för åtkomst till den här mappen är begränsade till den aktuella användaren och administratörerna. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) här.)

Du `appInsights-node` kan åsidosätta mappsökvägen genom att ändra körnings värdet för den statiska variabeln som `Sender.TEMPDIR_PREFIX` finns i [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>Java Script (webbläsare)

[HTML5-session lagring](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) används för att bevara data. Två separata buffertar används: `AI_buffer` och `AI_sent_buffer` . Telemetri som är batch-och väntar på att skickas lagras i `AI_buffer` . Telemetri som precis har skickats placeras tills inmatnings `AI_sent_buffer` servern svarar på att den har tagits emot. När telemetri har tagits emot tas den bort från alla buffertar. När ett tillfälligt fel (till exempel en användare förlorar nätverks anslutningen) är telemetri kvar `AI_buffer` tills det har tagits emot eller så svarar inmatnings servern att telemetri är ogiltigt (felaktigt schema eller för gammalt, till exempel).

Telemetri-buffertar kan inaktive ras genom att ställa in [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) på `false` . När session Storage är inaktiverat, används en lokal matris i stället som beständig lagring. Eftersom JavaScript SDK körs på en klient enhet har användaren åtkomst till den här lagrings platsen via deras utvecklarverktyg.

### <a name="opencensus-python"></a>OpenCensus Python

Som standard använder du python SDK för den aktuella användaren `%username%/.opencensus/.azure/` . Behörigheter för åtkomst till den här mappen är begränsade till den aktuella användaren och administratörerna. (Se [implementering](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) här.) Mappen med dina sparade data kommer att namnges efter den python-fil som skapade Telemetrin.

Du kan ändra platsen för lagrings filen genom att skicka i `storage_path` parametern i konstruktorn för den exportör som du använder.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hur gör jag för att skicka data till Application Insights med TLS 1,2?

För att säkerställa säkerheten för data som överförs till Application Insights slut punkter rekommenderar vi att kunderna konfigurerar sina program att använda minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte** och branschen flyttas snabbt till överge support för dessa äldre protokoll. 

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har angett en [tids gräns på 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) för att inaktivera äldre versioner av TLS/SSL och uppgradera till säkrare protokoll. När Azure har tagit över äldre support, om ditt program/klienter inte kan kommunicera via minst TLS 1,2 skulle du inte kunna skicka data till Application Insights. Den metod du behöver för att testa och verifiera att ditt programs TLS-stöd varierar beroende på operativ system/plattform och språk/ramverk som används i programmet.

Vi rekommenderar inte att du uttryckligen anger att ditt program ska använda TLS 1,2 om det inte är nödvändigt eftersom det kan bryta säkerhets funktioner på plattforms nivå som gör att du automatiskt kan identifiera och dra nytta av nyare säkra protokoll när de blir tillgängliga, till exempel TLS 1,3. Vi rekommenderar att du utför en grundlig granskning av programmets kod för att söka efter hårdkoda av specifika TLS/SSL-versioner.

### <a name="platformlanguage-specific-guidance"></a>Vägledning för plattform/språk specifik

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
| Azure App Services  | Konfiguration kan krävas. | Support annonserades i april 2018. Läs [informationen om konfigurationen](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!).  |
| Azure-funktionsappar | Konfiguration kan krävas. | Support annonserades i april 2018. Läs [informationen om konfigurationen](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!). |
|.NET | Konfigurationen varierar beroende på version. | Detaljerad konfigurations information för .NET 4,7 och tidigare versioner finns i [de här anvisningarna](/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Statusövervakare | Stöds, konfiguration krävs | Statusövervakare är beroende av [OS-konfigurationen](/windows-server/security/tls/tls-registry-settings)av  +  [.net-konfigurationen](/dotnet/framework/network-programming/tls#support-for-tls-12) för att stödja TLS 1,2.
|Node.js |  Konfigurationen kan krävas i v-10.5.0. | Använd den [officiella Node.js TLS/SSL-dokumentationen](https://nodejs.org/api/tls.html) för valfri programspecifik konfiguration. |
|Java | Stöd för JDK-stöd för TLS 1,2 har lagts till i [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) och [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 använder [TLS 1,2 som standard](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-distributioner tenderar att förlita sig på [openssl](https://www.openssl.org) för TLS 1,2-stöd.  | Kontrol lera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8,0-10 | Stöds och är aktiverat som standard. | För att bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och är aktiverat som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men är inte aktiverat som standard. | På sidan [Transport Layer Security (TLS) register inställningar](/windows-server/security/tls/tls-registry-settings) finns mer information om hur du aktiverar.  |
| Windows Server 2008 SP2 | Stöd för TLS 1,2 kräver en uppdatering. | Se [Uppdatera för att lägga till stöd för TLS 1,2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) i Windows Server 2008 SP2. |
|Windows Vista | Stöds inte. | Ej tillämpligt

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Kontrol lera vilken version av OpenSSL som din Linux-distribution körs på

Du kan kontrol lera vilken version av OpenSSL du har installerat genom att öppna terminalen och köra:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Kör en test-TLS 1,2-transaktion på Linux

Om du vill köra ett preliminärt test för att se om ditt Linux-system kan kommunicera via TLS 1,2. öppnar du terminalen och kör:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Person uppgifter som lagras i Application Insights

I [artikeln Application Insights personliga data](../logs/personal-data-mgmt.md) beskrivs det här problemet i djupet.

#### <a name="can-my-users-turn-off-application-insights"></a>Kan mina användare stänga av Application Insights?
Inte direkt. Vi tillhandahåller inte en växel som användarna kan använda för att stänga av Application Insights.

Du kan dock implementera en sådan funktion i ditt program. Alla SDK: er innehåller en API-inställning som inaktiverar telemetri-samlingen. 

## <a name="data-sent-by-application-insights"></a>Data som skickats av Application Insights
SDK: erna varierar mellan olika plattformar och det finns flera komponenter som du kan installera. (Se [Application Insights-översikt][start].) Varje komponent skickar olika data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasser för data som skickas i olika scenarier

| Din åtgärd | Insamlade data klasser (se nästa tabell) |
| --- | --- |
| [Lägga till Application Insights SDK i ett .NET-webbprojekt][greenbrown] |ServerContext<br/>Härleda<br/>Prestandaräknare<br/>Begäranden<br/>**Undantag**<br/>Session<br/>användare |
| [Installera Statusövervakare på IIS][redfield] |Beroenden<br/>ServerContext<br/>Härleda<br/>Prestandaräknare |
| [Lägga till Application Insights SDK i en Java-webbapp][java] |ServerContext<br/>Härleda<br/>Förfrågan<br/>Session<br/>användare |
| [Lägg till Java Script SDK på webb sidan][client] |ClientContext <br/>Härleda<br/>Sida<br/>ClientPerf<br/>Ajax |
| [Definiera standard egenskaper][apiproperties] |**Egenskaper** för alla standard-och anpassade händelser |
| [Anropa TrackMetric][api] |Numeriska värden<br/>**Egenskaper** |
| [Samtals spår *][api] |Händelse namn<br/>**Egenskaper** |
| [Anropa TrackException][api] |**Undantag**<br/>Stackdump<br/>**Egenskaper** |
| SDK kan inte samla in data. Exempel: <br/> -Det går inte att komma åt perf-räknare<br/> – undantag i telemetri initierare |SDK-diagnostik |

För [SDK: er för andra plattformar][platforms], se deras dokument.

#### <a name="the-classes-of-collected-data"></a>Klasser för insamlade data

| Insamlad data klass | Inkluderar (inte en fullständig lista) |
| --- | --- |
| **Egenskaper** |**Alla data som bestäms av din kod** |
| DeviceContext |`Id`, IP, språk, enhets modell, nätverk, nätverks typ, OEM-namn, skärmupplösning, roll instans, roll namn, enhets typ |
| ClientContext |OS, språk, språk, nätverk, fönster upplösning |
| Session |`session id` |
| ServerContext |Dator namn, språk, operativ system, enhet, användarsession, användar kontext, åtgärd |
| Härleda |Geo-plats från IP-adress, tidsstämpel, OS, webbläsare |
| Mått |Metric-namn och-värde |
| Händelser |Händelse namn och-värde |
| PageViews |URL och sid namn eller skärm namn |
| Klient prestanda |URL/sidnamn, webb läsar inläsnings tid |
| Ajax |HTTP-anrop från webb sida till Server |
| Begäranden |URL, varaktighet, svarskod |
| Beroenden |Typ (SQL, HTTP,...), anslutnings sträng eller URI, Sync/async, varaktighet, lyckades, SQL-uttryck (med Statusövervakare) |
| **Undantag** |Typ, **meddelande**, anrops stackar, käll fil, rad nummer, `thread id` |
| Krascher |`Process id`, `parent process id` , `crash thread id` ; program korrigering, `id` , bygge;  undantags typ, adress, orsak; fördunklade symboler och register, start-och slut adresser, namn och sökväg för binärfiler, CPU-typ |
| Spårning |**Meddelande** -och allvarlighets nivå |
| Prestandaräknare |Processor tid, tillgängligt minne, begär ande frekvens, undantags frekvens, processens privata byte, i/o-hastighet, varaktighet för begäran, Kölängd för begäran |
| Tillgänglighet |Svars kod för webbtest, varaktighet för varje test steg, testnamn, tidsstämpel, framgång, svars tid, test plats |
| SDK-diagnostik |Spårnings meddelande eller undantag |

Du kan [stänga av vissa data genom att redigera ApplicationInsights.config][config]

> [!NOTE]
> Klientens IP-adress används för att härleda geografisk plats, men som standard är IP-data inte längre lagrade och alla nollor skrivs till det associerade fältet. Om du vill veta mer om personlig data hantering rekommenderar vi den här [artikeln](../logs/personal-data-mgmt.md#application-data). Om du behöver lagra IP-Datadata kommer vår [artikel för IP-adresser](./ip-collection.md) att vägleda dig genom dina alternativ.

## <a name="can-i-modify-or-update-data-after-it-has-been-collected"></a>Kan jag ändra eller uppdatera data när de har samlats in?

Nej, data är skrivskyddade och kan bara tas bort via rensnings funktionen. Mer information finns [i rikt linjer för person uppgifter som lagras i Log Analytics och Application Insights](../logs/personal-data-mgmt.md#delete).

## <a name="credits"></a>Krediter
Den här produkten innehåller GeoLite2-data som skapats av MaxMind, som är tillgängliga från [https://www.maxmind.com](https://www.maxmind.com) .



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-get-started.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

