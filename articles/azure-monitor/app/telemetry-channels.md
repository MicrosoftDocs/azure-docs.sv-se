---
title: Telemetri kanaler i Azure Application Insights | Microsoft Docs
description: 'Så här anpassar du telemetri kanaler i Azure Application Insights SDK: er för .NET och .NET Core.'
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937356"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetri kanaler i Application Insights

Telemetri kanaler är en integrerad del av [Azure Application Insights SDK](./app-insights-overview.md): er. De hanterar buffring och överföring av telemetri till Application Insights tjänsten. .NET-och .NET Core-versionerna av SDK: erna har två inbyggda telemetri-kanaler: `InMemoryChannel` och `ServerTelemetryChannel` . I den här artikeln beskrivs varje kanal i detalj, inklusive hur du anpassar kanal beteendet.

## <a name="what-are-telemetry-channels"></a>Vad är kanaler för telemetri?

Telemetri kanaler är ansvariga för att buffra telemetri-objekt och skicka dem till den Application Insights tjänsten där de lagras för frågor och analyser. En telemetri kanal är en klass som implementerar [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) gränssnittet.

`Send(ITelemetry item)`Metoden för en telemetri-kanal anropas efter att alla telemetri-och telemetri-processorer har anropats. Det innebär att alla objekt som släppts av en telemetri-processor inte når kanalen. `Send()` skickar vanligt vis inte objekten till Server delen direkt. Normalt buffrar den dem i minnet och skickar dem i batchar för effektiv överföring.

[Live Metrics Stream](live-stream.md) har också en anpassad kanal som kan utnyttja Direktsänd strömning av telemetri. Den här kanalen är oberoende av den ordinarie telemetri-kanalen och det här dokumentet gäller inte för det.

## <a name="built-in-telemetry-channels"></a>Inbyggda telemetri kanaler

Application Insights .NET-och .NET Core SDK: er levereras med två inbyggda kanaler:

* `InMemoryChannel`: En lätt kanal som buffrar objekt i minnet tills de har skickats. Objekt buffras i minnet och töms var 30: e sekund, eller när 500 objekt buffras. Den här kanalen ger låg Tillförlitlighets garantier eftersom den inte försöker skicka telemetri igen efter ett fel. Den här kanalen behåller inte heller objekt på disken, så alla objekt som inte skickats går förlorade permanent när programmet stängs av (korrekt eller inte). Den här kanalen implementerar en `Flush()` metod som kan användas för att tvinga fram alla InMemory-telemetridata synkront. Den här kanalen passar bra för program som körs under kort där en synkron tömning är idealisk.

    Den här kanalen är en del av det större Microsoft. ApplicationInsights NuGet-paketet och är standard kanalen som används av SDK när inget annat har kon figurer ATS.

* `ServerTelemetryChannel`: En mer avancerad kanal som har principer för återförsök och möjlighet att lagra data på en lokal disk. Den här kanalen försöker skicka telemetri igen om tillfälliga fel inträffar. Den här kanalen använder också lokal disk lagring för att lagra objekt på disk under nätverks avbrott eller volymer med hög telemetri. På grund av dessa metoder för omförsök och lokal disk lagring anses denna kanal vara mer tillförlitlig och rekommenderas för alla produktions scenarier. Den här kanalen är standard för [ASP.net](./asp-net.md) och [ASP.net Core](./asp-net-core.md) program som har kon figurer ATS enligt den officiella dokumentationen. Den här kanalen är optimerad för Server scenarier med tids krävande processer. [`Flush()`](#which-channel-should-i-use)Metoden som implementeras av den här kanalen är inte synkron.

    Den här kanalen levereras som Microsoft. ApplicationInsights. Windows Server. TelemetryChannel NuGet-paketet och hämtas automatiskt när du använder antingen Microsoft. ApplicationInsights. Web eller Microsoft. ApplicationInsights. AspNetCore NuGet-paketet.

## <a name="configure-a-telemetry-channel"></a>Konfigurera en telemetri kanal

Du konfigurerar en telemetri-kanal genom att ställa in den på den aktiva telemetri-konfigurationen. För ASP.NET-program innebär konfigurationen att ställa in telemetri kanal instansen till `TelemetryConfiguration.Active` eller genom att ändra `ApplicationInsights.config` . För ASP.NET Core-program innebär konfigurationen att lägga till kanalen i behållaren för beroende inmatning.

I följande avsnitt visas exempel på hur du konfigurerar `StorageFolder` inställningen för kanalen i olika program typer. `StorageFolder` är bara en av de konfigurerbara inställningarna. En fullständig lista över konfigurations inställningar finns i [avsnittet Inställningar](#configurable-settings-in-channels) längre fram i den här artikeln.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration med hjälp av ApplicationInsights.config för ASP.NET-program

I följande avsnitt från [ApplicationInsights.config](configuration-with-applicationinsights-config.md) visas den `ServerTelemetryChannel` kanal som kon figurer ATS med `StorageFolder` inställningen till en anpassad plats:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguration i kod för ASP.NET-program

Följande kod ställer in en ServerTelemetryChannel-instans med `StorageFolder` en anpassad plats. Lägg till den här koden i början av programmet, vanligt vis i `Application_Start()` metoden i global. aspx. cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguration i kod för ASP.NET Core program

Ändra- `ConfigureServices` metoden för `Startup.cs` klassen som visas här:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Att konfigurera kanalen med hjälp av `TelemetryConfiguration.Active` rekommenderas inte för ASP.net Core program.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguration i kod för program i .NET/.NET Core-konsol

För-konsol program är koden samma för både .NET och .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Drift information om ServerTelemetryChannel

`ServerTelemetryChannel` lagrar inkommande objekt i en minnes intern buffert. Objekten serialiseras, komprimeras och lagras i en `Transmission` instans var 30: e sekund, eller när 500-objekt har buffrats. En enskild `Transmission` instans innehåller upp till 500 objekt och representerar en batch med telemetri som skickas via ett enda https-anrop till Application Insights tjänsten.

Som standard kan högst 10 `Transmission` instanser skickas parallellt. Om telemetri kommer till snabbare priser, eller om nätverket eller Application Insights Server delen är långsamt, `Transmission` lagras instanser i minnet. Standard kapaciteten för den här InMemory- `Transmission` bufferten är 5 MB. När minnes kapaciteten har överskridits `Transmission` lagras instanser på den lokala disken upp till en gräns på 50 MB. `Transmission` instanser lagras även på den lokala disken när det finns nätverks problem. Endast de objekt som lagras på en lokal disk överleva en program krasch. De skickas när programmet startar igen.

## <a name="configurable-settings-in-channels"></a>Konfigurerbara inställningar i kanaler

En fullständig lista över konfigurerbara inställningar för varje kanal finns i:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Här följer de vanligaste inställningarna för `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: Den maximala mängd minne, i byte, som används av kanalen för att buffra överföringarna i minnet. När den här kapaciteten nås lagras nya objekt direkt på den lokala disken. Standardvärdet är 5 MB. Att ange ett högre värde leder till mindre disk användning, men kom ihåg att objekt i minnet går förlorade om programmet kraschar.

1. `MaxTransmissionSenderCapacity`: Det maximala antalet `Transmission` instanser som ska skickas till Application Insights på samma tidpunkt. Standardvärdet är 10. Den här inställningen kan konfigureras till ett högre antal, vilket rekommenderas när en stor mängd telemetri skapas. Hög volym uppstår vanligt vis vid belastnings testning eller när sampling är inaktiverat.

1. `StorageFolder`: Mappen som används av kanalen för att lagra objekt på disken efter behov. I Windows används antingen% LOCALAPPDATA% eller% TEMP% om ingen annan sökväg anges explicit. I andra miljöer än Windows måste du ange en giltig plats eller telemetri lagras inte på den lokala disken.

## <a name="which-channel-should-i-use"></a>Vilken kanal ska jag använda?

`ServerTelemetryChannel` rekommenderas för de flesta produktions scenarier som omfattar tids krävande program. `Flush()`Metoden som implementeras av `ServerTelemetryChannel` är inte synkron och garanterar inte heller att skicka alla väntande objekt från minnet eller disken. Om du använder den här kanalen i scenarier där programmet ska stängas av rekommenderar vi att du introducerar en fördröjning efter att ha anropat `Flush()` . Den exakta fördröjning som du kanske behöver är förutsägbar. Det beror på faktorer som hur många objekt eller `Transmission` instanser som finns i minnet, hur många som finns på disk, hur många som skickas till Server delen och om kanalen är i mitten av exponentiella scenarier.

Om du behöver göra en synkron tömning rekommenderar vi att du använder `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garanterar Application Insights-kanalen för telemetri? I så fall, vilka scenarier kan telemetri gå förlorade?

Det korta svaret är att ingen av de inbyggda kanalerna ger en garanti för att en transaktions typ garanterar telemetri till Server delen. `ServerTelemetryChannel` är mer avancerad jämfört med `InMemoryChannel` för tillförlitlig leverans, men det gör också bara ett bästa försök att skicka telemetri. Telemetri kan fortfarande gå förlorat i flera situationer, inklusive dessa vanliga scenarier:

1. Objekt i minnet går förlorade när programmet kraschar.

1. Telemetri förloras under utökade perioder av nätverks problem. Telemetri lagras på en lokal disk under nätverks avbrott eller när problem uppstår med Application Insights Server del. Men objekt som är äldre än 48 timmar ignoreras.

1. Standard disk platserna för lagring av telemetri i Windows är% LOCALAPPDATA% eller% TEMP%. Dessa platser är vanligt vis lokala för datorn. Om programmet migreras fysiskt från en plats till ett annat, går all telemetri som lagras på den ursprungliga platsen förlorad.

1. I Web Apps på Windows är standard lagrings platsen för disk lagring D:\local\LocalAppData. Den här platsen är inte beständig. Den rensas ut i appars omstarter, skalnings-och andra åtgärder, vilket leder till förlust av telemetri som lagras där. Du kan åsidosätta standardvärdet och ange lagring till en sparad plats som D:\home. Sådana beständiga platser kan dock hanteras av Fjärrlagring och kan vara långsamma.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Fungerar ServerTelemetryChannel på andra system än Windows?

Även om namnet på paketet och namn området innehåller "Windows Server", stöds denna kanal på andra system än Windows, med följande undantag. På andra system än Windows skapar kanalen ingen lokal lagringsmapp som standard. Du måste skapa en lokal lagringsmapp och konfigurera kanalen för att använda den. När lokal lagring har kon figurer ATS fungerar kanalen på samma sätt på alla system.

> [!NOTE]
> Med version 2.15.0-beta3 och större lokal lagring skapas nu automatiskt för Linux, Mac och Windows. För icke-Windows-system skapar SDK: n automatiskt en lokal lagringsmapp baserat på följande logik:
> - `${TMPDIR}` – Om `${TMPDIR}` miljövariabeln anges används den här platsen.
> - `/var/tmp` – om den föregående platsen inte finns försöker vi `/var/tmp` .
> - `/tmp` – om båda de föregående platserna inte finns provar vi `tmp` . 
> - Om ingen av dessa platser finns lokalt lagras inte och manuell konfiguration krävs fortfarande. [För fullständig implementerings information](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Skapar SDK temporär lokal lagring? Är data krypterade vid lagring?

SDK lagrar telemetri-objekt i lokal lagring under nätverks problem eller under begränsning. Dessa data är inte krypterade lokalt.

För Windows-system skapar SDK: n automatiskt en tillfällig lokal mapp i katalogen% TEMP% eller% LOCALAPPDATA% och begränsar åtkomsten till administratörer och den aktuella användaren.

För andra system än Windows skapas ingen lokal lagring automatiskt av SDK: n och därför lagras inga data lokalt som standard.

> [!NOTE]
> Med version 2.15.0-beta3 och större lokal lagring skapas nu automatiskt för Linux, Mac och Windows. 

 Du kan skapa en lagrings katalog själv och konfigurera kanalen för att använda den. I det här fallet är du ansvarig för att se till att katalogen är skyddad.
Läs mer om [data skydd och sekretess](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK för öppen källkod
Precis som varje SDK för Application Insights, är kanaler öppen källkod. Läs och bidra till koden eller rapportera problem på [den officiella GitHub-lagrings platsen](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nästa steg

* [Sampling](./sampling.md)
* [SDK-felsökning](./asp-net-troubleshoot-no-data.md)

