---
title: Cache-ASP.NET för sessionstillstånd
description: Lär dig hur du lagrar ASP.NET-sessionstillstånd i minnet med Azure cache för Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544536"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Provider av ASP.NET-sessionstillstånd för Azure Cache for Redis

Azure cache för Redis innehåller en provider för sessionstillstånd som du kan använda för att lagra sessionens tillstånd i minnet med Azure cache för Redis i stället för en SQL Server databas. Om du vill använda providern för sessionstillstånd för cachelagring måste du först konfigurera cachen och sedan konfigurera ditt ASP.NET-program för cachelagring med Azure-cache för Redis NuGet-paketet. För ASP.NET Core program, Läs [session-och tillstånds hantering i ASP.net Core](/aspnet/core/fundamentals/app-state).

Det är ofta inte praktiskt i en verklig molnbaserad app att undvika att lagra någon form av tillstånd för en användarsession, men vissa metoder påverkar prestanda och skalbarhet mer än andra. Om du behöver lagra tillstånd är den bästa lösningen att behålla mängden State Small och lagra den i cookies. Om det inte är möjligt är nästa bästa lösning att använda ASP.NET-sessionstillstånd med en provider för distribuerad minnes intern cache. Den värsta lösningen från en prestanda-och skalbarhet är att använda en databas som har säkerhetskopierats i ett sessionstillstånd. Det här avsnittet innehåller information om hur du använder ASP.NET-providern för sessionstillstånd för Azure cache för Redis. Information om andra sessionstillstånds alternativ finns i [ASP.net alternativ för sessionstillstånd](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Lagra ASP.NET-sessionstillstånd i cachen

Om du vill konfigurera ett klient program i Visual Studio med hjälp av NuGet-paketet Azure cache för Redis session State, klickar du på **NuGet Package Manager**, **Package Manager-konsolen** på menyn **verktyg** .

Kör följande kommando från fönstret `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Om du använder kluster funktionen från Premium-nivån måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre, annars genereras ett undantag. Att flytta till 2.0.1 eller högre är en avbrytande ändring. Mer information finns i [v 2.0.0-brytande ändrings information](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Vid uppdateringen av den här artikeln är den aktuella versionen av det här paketet 2.2.3.
> 
> 

Redis NuGet-paketet för sessionstillstånd har ett beroende av paketet StackExchange. Redis. StrongName. Om paketet StackExchange. Redis. StrongName inte finns i projektet är det installerat.

>[!NOTE]
>Förutom det starkt namngivna StackExchange. Redis. StrongName-paketet finns även StackExchange. Redis-versionen som inte är starkt namngiven. Om ditt projekt använder den icke-starkt namngivna StackExchange. Redis-versionen måste du avinstallera den, annars får du namn konflikter i projektet. Mer information om dessa paket finns i [Konfigurera .net-cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

NuGet-paketet hämtar och lägger till de sammansättnings referenser som krävs och lägger till följande avsnitt i web.config-filen. Det här avsnittet innehåller den konfiguration som krävs för ditt ASP.NET-program för att använda Azure-cache för Redis-providern för sessionstillstånd.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Avsnittet kommenterat innehåller ett exempel på attribut och exempel inställningar för varje attribut.

Konfigurera attributen med värdena från ditt cache-blad i Microsoft Azure-portalen och konfigurera de andra värdena efter behov. Anvisningar om hur du kommer åt dina cache-egenskaper finns i [Konfigurera Azure cache för Redis-inställningar](cache-configure.md#configure-azure-cache-for-redis-settings).

* **värd** – ange din cache-slutpunkt.
* **port** – Använd antingen en icke-TLS/SSL-port eller TLS/SSL-port, beroende på TLS-inställningarna.
* **accessKey** – Använd antingen den primära eller sekundära nyckeln för din cache.
* **SSL** – sant om du vill skydda cache-/klient kommunikation med TLS; annars falskt. Se till att ange rätt port.
  * Icke-TLS-porten är inaktive rad som standard för nya cacheminnen. Ange True för att den här inställningen ska använda TLS-porten. Mer information om hur du aktiverar icke-TLS-porten finns i avsnittet [åtkomst portar](cache-configure.md#access-ports) i avsnittet [Konfigurera ett cacheminne](cache-configure.md) .
* **throwOnError** – sant om du vill att ett undantag ska genereras om det uppstår ett fel eller falskt om du vill att åtgärden ska Miss lyckas tyst. Du kan kontrol lera om det finns ett problem genom att kontrol lera den statiska Microsoft. Web. Redis. RedisSessionStateProvider. LastException-egenskapen. Standardvärdet är true.
* **retryTimeoutInMilliseconds** – åtgärder som inte kan utföras på nytt under det här intervallet anges i millisekunder. Det första omförsöket sker efter 20 millisekunder och nya försök görs varje sekund tills retryTimeoutInMilliseconds-intervallet upphör att gälla. Omedelbart efter det här intervallet görs ett nytt försök att utföra åtgärden en sista gång. Om åtgärden fortfarande Miss lyckas, genereras undantaget tillbaka till anroparen, beroende på inställningen för throwOnError. Standardvärdet är 0, vilket innebär inga återförsök.
* **databaseId** – anger vilken databas som ska användas för cachelagring av utdata. Om inget värde anges används standardvärdet 0.
* **applicationName** – nycklarna lagras i Redis som `{<Application Name>_<Session ID>}_Data` . Detta namngivnings schema gör att flera program kan dela samma Redis-instans. Den här parametern är valfri, och om du inte anger den används inget standardvärde.
* **connectionTimeoutInMilliseconds** – med den här inställningen kan du åsidosätta inställningen ConnectTimeout i stackexchange. Redis-klienten. Om inget värde anges används standard connectTimeout-inställningen 5000. Mer information finns i [konfigurations modellen för stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – med den här inställningen kan du åsidosätta inställningen SyncTimeout i stackexchange. Redis-klienten. Om inget värde anges används standard syncTimeout-inställningen 1000. Mer information finns i [konfigurations modellen för stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – med den här inställningen kan du ange anpassad serialisering av sessionens innehåll som skickas till Redis. Den typ som anges måste implementera `Microsoft.Web.Redis.ISerializer` och måste deklarera en offentlig parameter lös konstruktor. Som standard  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` används.

Mer information om dessa egenskaper finns i det ursprungliga blogg inlägget för att [presentera ASP.net-providern för Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Glöm inte att kommentera ut avsnittet standard InProc-Provider för sessionstillstånd i din web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

När dessa steg har utförts är ditt program konfigurerat för att använda Azure-cachen för Redis för sessionstillstånd. När du använder sessionstillstånd i ditt program lagras det i en Azure-cache för Redis-instans.

> [!IMPORTANT]
> Data som lagras i cachen måste kunna serialiseras, till skillnad från de data som kan lagras i standardprovidern för ASP.NET i minnet. När providern för sessionstillstånd för Redis används, se till att de data typer som lagras i sessionstillstånd är serialiserbar.
> 
> 

## <a name="aspnet-session-state-options"></a>Tillstånds alternativ för ASP.NET

* I lagringsprovider för sessionstillstånd – den här providern lagrar sessionstillståndet i minnet. Fördelen med att använda den här providern är att det är enkelt och snabbt. Du kan dock inte skala dina Web Apps om du använder i minnes leverantören eftersom den inte är distribuerad.
* Provider för SQL Server-sessionstillstånd – denna provider lagrar sessionstillståndet i SQL Server. Använd den här providern om du vill lagra sessionstillståndet i beständig lagring. Du kan skala din webbapp, men att använda SQL Server för session har en prestanda påverkan på din webbapp. Du kan också använda providern med en [InMemory OLTP-konfiguration](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) för att förbättra prestanda.
* Distribuerat i tillstånds leverantör för sessionstillstånd, till exempel Azure cache för Redis-providern för sessionstillstånd – den här leverantören ger dig det bästa av båda världar. Din webbapp kan ha en enkel, snabb och skalbar session för sessionstillstånd. Eftersom den här providern lagrar sessionstillståndet i en cache måste din app ta hänsyn till alla egenskaper som är associerade med att kommunicera med en distribuerad minnes-cache, till exempel tillfälliga nätverks haverier. Bästa praxis om hur du använder cache finns i [Guide för cachelagring](/azure/architecture/best-practices/caching) från Microsoft patterns & praxis [Azure Cloud Application design och implementerings vägledning](https://github.com/mspnp/azure-guidance).

Mer information om sessionstillstånd och andra bästa metoder finns i [metod tips för webb utveckling (skapa Real-World molnappar med Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Providers för sessionstillstånd från tredje part

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache antändning](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Nästa steg

Kolla in [ASP.net output cache Provider för Azure cache för Redis](cache-aspnet-output-cache-provider.md).