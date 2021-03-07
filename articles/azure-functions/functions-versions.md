---
title: Översikt över Azure Functions körnings versioner
description: Azure Functions stöder flera versioner av körnings miljön. Lär dig mer om skillnaderna mellan dem och hur du väljer det som passar dig bäst.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: b37cf33a96452f9f3e86f853d3d87fd3b4b3879c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431862"
---
# <a name="azure-functions-runtime-versions-overview"></a>Översikt över Azure Functions körnings versioner

Azure Functions stöder för närvarande tre versioner av körnings värden: 3. x, 2. x och 1. x. Alla tre versioner stöds i produktions scenarier.  

> [!IMPORTANT]
> Version 1. x är i underhålls läge och stöder bara utveckling i Azure Portal, Azure Stack Hub-portalen eller lokalt på Windows-datorer. Förbättringar finns bara i senare versioner. 

I den här artikeln beskrivs några skillnader mellan olika versioner, hur du kan skapa varje version och hur du ändrar versioner.

## <a name="languages"></a>Språk

Från och med version 2. x använder körnings modellen en språk utöknings modell och alla funktioner i en Function-app måste dela samma språk. Språket i funktioner i en Function-app väljs när du skapar appen och underhålls i inställningen [Function \_ Worker \_ runtime](functions-app-settings.md#functions_worker_runtime) . 

Följande tabell visar vilka programmeringsspråk som för närvarande stöds i varje körnings version.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Kör på en angiven version

Som standard har funktions appar som skapats i Azure Portal och av Azure CLI angetts till version 3. x. Du kan ändra den här versionen efter behov. Du kan bara nedgradera körnings versionen till 1. x när du har skapat din Function-app, men innan du lägger till några funktioner.  Att flytta mellan 2. x och 3. x tillåts även med appar som har befintliga funktioner. Innan du flyttar en app med befintliga funktioner från 2. x till 3. x måste du vara medveten om eventuella överlappande [ändringar mellan 2 x och 3. x](#breaking-changes-between-2x-and-3x). 

Innan du gör en ändring i huvud versionen av körnings miljön bör du först testa din befintliga kod genom att distribuera till en annan Function-app som körs på den senaste huvud versionen. Den här testningen hjälper till att kontrol lera att den fungerar som den ska efter uppgraderingen. 

Nedgradering från v3. x till v2. x stöds inte. När det är möjligt bör du alltid köra dina appar på den senaste versionen av Functions-körningen som stöds. 

### <a name="changing-version-of-apps-in-azure"></a>Ändra versionen av appar i Azure

Den version av Functions runtime som används av publicerade appar i Azure styrs av [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) program inställningen. Följande värden för större körnings versioner stöds:

| Värde | Körnings mål |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2x |
| `~1` | 1.x |

>[!IMPORTANT]
> Ändra inte den här inställningen godtyckligt, eftersom andra inställnings ändringar och ändringar i funktions koden kan krävas.

Mer information finns i [så här riktar du Azure Functions runtime-versioner](set-runtime-version.md).  

### <a name="pinning-to-a-specific-minor-version"></a>Fästa till en speciell del version

För att lösa problem med att din Function-App körs på den senaste huvud versionen måste du fästa appen till en särskild lägre version. Det ger dig tid att få din app att köras korrekt på den senaste huvud versionen. Hur du fäster till en lägre version skiljer sig mellan Windows och Linux. Mer information finns i [så här riktar du Azure Functions runtime-versioner](set-runtime-version.md).

Äldre del versioner tas regelbundet bort från funktioner. För de senaste nyheterna om Azure Functions-versioner, inklusive borttagning av vissa äldre versioner, övervaka [Azure App Service meddelanden](https://github.com/Azure/app-service-announcements/issues). 

### <a name="pinning-to-version-20"></a>Fäst till version ~ 2,0

.NET Function-appar som körs på version 2. x ( `~2` ) uppgraderas automatiskt till att köras på .net core 3,1, vilket är en långsiktig support version av .net Core 3. Genom att köra dina .NET-funktioner på .NET Core 3,1 kan du dra nytta av de senaste säkerhets uppdateringarna och produkt förbättringarna. 

Alla Functions-appar som fästs för `~2.0` fortsätter att köras på .net Core 2,2, som inte längre tar emot säkerhet och andra uppdateringar. Mer information finns i avsnittet [funktioner v2. x](functions-dotnet-class-library.md#functions-v2x-considerations).   

## <a name="migrating-from-2x-to-3x"></a>Migrerar från 2. x till 3. x

Azure Functions version 3. x är hög baklänges som är kompatibel med version 2. x.  Många appar bör kunna uppgraderas på ett säkert sätt till 3. x utan några kod ändringar.  När du flyttar till 3. x rekommenderas du att köra omfattande tester innan du ändrar huvud versionen i produktions program.

### <a name="breaking-changes-between-2x-and-3x"></a>Bryta ändringar mellan 2 x och 3. x

Följande är ändringar som du måste känna till innan du uppgraderar en 2. x-app till 3. x.

#### <a name="javascript"></a>JavaScript

* Utgående bindningar som tilldelats genom `context.done` eller RETUR värden beter sig nu på samma sätt som i `context.bindings` .

* Timer-utlösarens objekt är camelCase i stället för PascalCase

* Event Hub utlösta funktioner med `dataType` Binary får en matris i `binary` stället för `string` .

* Nytto lasten för HTTP-begäran kan inte längre nås via `context.bindingData.req` .  Den kan fortfarande kommas åt som en indataparameter, `context.req` och i `context.bindings` .

* Node.js 8 stöds inte längre och körs inte i 3. x-funktioner.

#### <a name="net-core"></a>.NET Core

De största skillnaderna mellan versioner när du kör funktioner i .NET-klass bibliotek är .NET Core-körningsmiljön. Functions version 2. x är utformad för att köras på .NET Core 2,2 och version 3. x har utformats för att köras på .NET Core 3,1.  

* [Synkrona Server åtgärder är inaktiverade som standard](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

* Viktiga ändringar som introducerades av .NET core i [version 3,1](/dotnet/core/compatibility/3.1) och [version 3,0](/dotnet/core/compatibility/3.0), som inte är speciella för funktioner, men som kan fortfarande påverka din app.

>[!NOTE]
>På grund av support problem med .NET Core 2,2 körs Function-appar som är fästa på version 2 ( `~2` ) i grunden i .net Core 3,1. Läs mer i [funktions läge v2. x](functions-dotnet-class-library.md#functions-v2x-considerations).

## <a name="migrating-from-1x-to-later-versions"></a>Migrera från 1. x till senare versioner

Du kan välja att migrera en befintlig app som skrivits för att använda version 1. x runtime för att istället använda en nyare version. De flesta ändringar du behöver göra är relaterade till ändringar i språk körningen, till exempel C# API-ändringar mellan .NET Framework 4,7 och .NET Core. Du måste också kontrol lera att din kod och dina bibliotek är kompatibla med den språk körning som du väljer. Slutligen är det viktigt att notera eventuella ändringar i utlösare, bindningar och funktioner som marker ATS nedan. För bästa migrerings resultat bör du skapa en ny function-app i en ny version och port din befintliga version 1. x-funktions kod i den nya appen.  

Även om det är möjligt att göra en "på plats"-uppgradering genom att uppdatera appen manuellt genom att gå från 1. x till en högre version innehåller några större ändringar. I C# ändras exempelvis fel söknings objektet från `TraceWriter` till `ILogger` . Genom att skapa ett nytt version 3. x-projekt börjar du med uppdaterade funktioner baserat på de senaste version 3. x-mallarna.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Ändringar i utlösare och bindningar efter version 1. x

Från och med version 2. x måste du installera tilläggen för vissa utlösare och bindningar som används av funktionerna i din app. Det enda undantaget för HTTP-och timer-utlösare, vilket inte kräver ett tillägg.  Mer information finns i [Registrera och installera bindnings tillägg](./functions-bindings-register.md).

Det finns också några ändringar i *function.jspå* eller attribut för funktionen mellan versioner. Till exempel är Event Hub- `path` egenskapen nu `eventHubName` . I den [befintliga bindnings tabellen](#bindings) finns länkar till dokumentation för varje bindning.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Ändringar i funktioner efter version 1. x

Några funktioner har tagits bort, uppdaterats eller ersatts efter version 1. x. I det här avsnittet beskrivs de ändringar du ser i senare versioner när du har använt version 1. x.

I version 2. x gjordes följande ändringar:

* Nycklar för att anropa HTTP-slutpunkter lagras alltid krypterade i Azure Blob Storage. I version 1. x lagrades nycklar i Azure File Storage som standard. När du uppgraderar en app från version 1. x till version 2. x återställs befintliga hemligheter som finns i fil lagringen.

* Version 2. x-körningsmiljön innehåller inget inbyggt stöd för webhook-providrar. Den här ändringen gjordes för att förbättra prestandan. Du kan fortfarande använda HTTP-utlösare som slut punkter för Webhooks.

* Värd konfigurations filen (host.jspå) måste vara tom eller ha strängen `"version": "2.0"` .

* För att förbättra övervakningen ersätts instrument panelen WebJobs i portalen, som använde [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) inställningen med Azure Application insikter som använder [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) inställningen. Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

* Alla funktioner i en Function-app måste dela samma språk. När du skapar en Function-app måste du välja en körnings stack för appen. Körnings stacken anges av [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) värdet i program inställningar. Detta krav har lagts till för att förbättra utrymme och start tid. När du utvecklar lokalt måste du också inkludera den här inställningen i [local.settings.jspå filen](functions-run-local.md#local-settings-file).

* Standard tids gränsen för funktioner i en App Service plan ändras till 30 minuter. Du kan manuellt ändra tillbaka tids gränsen till obegränsad med hjälp av [functionTimeout](functions-host-json.md#functiontimeout) -inställningen i host.jspå.

* HTTP-samtidighets begränsning implementeras som standard för förbruknings plan funktioner, med standardvärdet 100 samtidiga begär Anden per instans. Du kan ändra detta i [`maxConcurrentRequests`](functions-host-json.md#http) inställningen i host.jsi filen.

* På grund av [begränsningar i .net Core](https://github.com/Azure/azure-functions-host/issues/3414)har stöd för F # script-funktioner (. FSX) tagits bort. Kompilerade F # functions (. FS) stöds fortfarande.

* URL-formatet för Event Grid trigger-Webhooks har ändrats till `https://{app}/runtime/webhooks/{triggerName}` .

### <a name="locally-developed-application-versions"></a>Lokalt utvecklade program versioner

Du kan göra följande uppdateringar för att Function-appar ska ändra mål versionerna lokalt.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio runtime-versioner

I Visual Studio väljer du kör tids versionen när du skapar ett projekt. Azure Functions verktyg för Visual Studio stöder de tre större körnings versionerna. Rätt version används vid fel sökning och publicering baserat på projekt inställningar. Versions inställningarna definieras i `.csproj` filen i följande egenskaper:

##### <a name="version-3x"></a>Version 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x och .NET kräver `Microsoft.NET.Sdk.Functions` att tillägget är minst `3.0.0` .

##### <a name="version-2x"></a>Version 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-1x"></a>Version 1. x

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Uppdaterar 2. x-appar till 3. x i Visual Studio

Du kan öppna en befintlig funktion med målet 2. x och flytta till 3. x genom att redigera `.csproj` filen och uppdatera värdena ovan.  Visual Studio hanterar körnings versioner automatiskt för dig baserat på projektets metadata.  Men det är möjligt om du aldrig har skapat en 3. x-app innan Visual Studio ännu inte har mallarna och körnings miljön för 3. x på din dator.  Detta kan presentera sig själv med ett fel som "det finns ingen funktion körning som matchar den version som anges i projektet".  Om du vill hämta de senaste mallarna och körnings miljön går du igenom upplevelsen för att skapa ett nytt funktions projekt.  När du kommer till sidan version och mall väljer du vänta tills Visual Studio har slutfört hämtningen av de senaste mallarna. När de senaste 3 mallarna för .NET Core är tillgängliga och visas kan du köra och felsöka alla projekt som har kon figurer ATS för version 3. x.

> [!IMPORTANT]
> Version 3. x-funktioner kan bara utvecklas i Visual Studio om du använder Visual Studio version 16,4 eller senare.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS-kod och Azure Functions Core Tools

[Azure Functions Core tools](functions-run-local.md) används för kommando rads utveckling och även av [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. För att utveckla mot version 3. x, installerar du version 3. x av kärn verktygen. Version 2. x-utveckling kräver version 2. x av kärn verktygen och så vidare. Mer information finns i [installera Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools).

För utveckling av Visual Studio-kod kan du också behöva uppdatera användar inställningen för `azureFunctions.projectRuntime` att matcha versionen av de installerade verktygen.  Den här inställningen uppdaterar även de mallar och språk som används när en funktion skapas.  Om du vill skapa appar i kan `~3` du uppdatera `azureFunctions.projectRuntime` användar inställningen till `~3` .

![Azure Functions tillägg för runtime-inställningar](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven och Java-appar

Du kan migrera Java-appar från version 2. x till 3. x genom [att installera 3. x-versionen av de kärn verktyg](functions-run-local.md#install-the-azure-functions-core-tools) som krävs för att köra lokalt.  När du har verifierat att appen fungerar som den ska lokalt i version 3. x uppdaterar du appens `POM.xml` fil för att ändra `FUNCTIONS_EXTENSION_VERSION` inställningen till `~3` , som i följande exempel:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Bindningar

Från och med version 2. x använder körningen en ny [bindnings modell för bindning](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som erbjuder följande fördelar:

* Stöd för bindnings tillägg från tredje part.

* Fri koppling av körning och bindningar. Den här ändringen tillåter att bindnings tilläggen versions och släpps oberoende av varandra. Du kan till exempel välja att uppgradera till en version av ett tillägg som förlitar sig på en nyare version av ett underliggande SDK.

* En miljö med en ljusare körning där endast de bindningar som används är kända och läses in av körnings miljön.

Med undantag för HTTP-och timer-utlösare måste alla bindningar uttryckligen läggas till i programmets projekt eller registreras i portalen. Mer information finns i [Registrera bindnings tillägg](./functions-bindings-expressions-patterns.md).

I följande tabell visas vilka bindningar som stöds i varje körnings version.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Så här riktar du Azure Functions runtime-versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)
