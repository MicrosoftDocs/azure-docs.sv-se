---
title: Förstå koncepten för enhets modellens lagrings plats | Microsoft Docs
description: Som en lösnings utvecklare eller IT-proffs kan du läsa om de grundläggande begreppen för enhets modellens lagrings plats.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582654"
---
# <a name="device-models-repository"></a>Lagrings plats för enhets modeller

Enhets modellens lagrings plats (DMR) gör det möjligt för enhets byggare att hantera och dela IoT Plug and Play enhets modeller. Enhets modellerna är JSON LD-dokument som definierats med hjälp av [DTDL (Digital Garns Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

DMR definierar ett mönster för att lagra DTDL-gränssnitt i en mappstruktur baserat på enhetens dubbla modell identifierare (DTMI). Du kan hitta ett gränssnitt i DMR genom att konvertera DTMI till en relativ sökväg. Till exempel `dtmi:com:example:Thermostat;1` översätts DTMI till `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Lagrings plats för offentliga enhets modeller

Microsoft är värd för en offentlig DMR med följande egenskaper:

- Granskade modeller. Microsoft granskar och godkänner alla tillgängliga gränssnitt med ett GitHub-validerings arbets flöde för pull-begäran (PR).
- Oföränderlighets.  När den har publicerats går det inte att uppdatera ett gränssnitt.
- Hyper-Scale. Microsoft tillhandahåller den infrastruktur som krävs för att skapa en säker, skalbar slut punkt där du kan publicera och använda enhets modeller.

## <a name="custom-device-models-repository"></a>Lagrings plats för anpassade enhets modeller

Använd samma DMR-mönster för att skapa en anpassad DMR i valfritt lagrings medium, t. ex. lokalt fil system eller anpassade HTTP-webbservrar. Du kan hämta enhets modeller från den anpassade DMR på samma sätt som från den offentliga DMR genom att ändra bas-URL: en som används för att få åtkomst till DMR.

> [!NOTE]
> Microsoft tillhandahåller verktyg för att validera enhets modeller i den offentliga DMR. Du kan återanvända dessa verktyg i anpassade lagrings platser.

## <a name="public-models"></a>Offentliga modeller

De offentliga enhets modeller som lagras i modell databasen är tillgängliga för alla att använda och integrera i sina program. Med offentliga enhets modeller kan du använda ett öppet eko system för enhets byggare och-utvecklare för att dela och återanvända sina IoT Plug and Play enhets modeller.

I avsnittet [publicera en modell](#publish-a-model) hittar du instruktioner om hur du publicerar en modell i modell databasen för att göra den offentlig.

Användare kan söka efter, söka i och Visa offentliga gränssnitt från den officiella [GitHub-lagringsplatsen](https://github.com/Azure/iot-plugandplay-models).

Alla gränssnitt i `dtmi` mapparna är också tillgängliga från den offentliga slut punkten [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Lösa modeller

Om du vill ha åtkomst till dessa gränssnitt via programmering kan du använda de `ModelsRepositoryClient` tillgängliga i NuGet-paketet [Azure. IoT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Den här klienten är konfigurerad som standard för att fråga de offentliga DMR som är tillgängliga på [devicemodels.Azure.com](https://devicemodels.azure.com/) och som kan konfigureras till alla anpassade lagrings platser.

Klienten accepterar en `DTMI` som inmatad och returnerar en ord lista med alla nödvändiga gränssnitt:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Förväntade utdata ska visa de `DTMI` tre gränssnitt som finns i beroende kedjan:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

`ModelsRepositoryClient`Kan konfigureras för att skicka frågor till en anpassad modell databas – tillgänglig via http (s) – och ange beroende matchning med någon av de tillgängliga `ModelDependencyResolution` :

- Inaktiverat Returnerar endast det angivna gränssnittet, utan något beroende.
- Aktiverat. Returnerar alla gränssnitt i beroende kedjan
- TryFromExpanded. Använd `.expanded.json` filen för att hämta de förberäknade beroendena 

> [!Tip] 
> De anpassade databaserna kanske inte exponerar `.expanded.json` filen, om inte klienten kommer att återgå till att bearbeta varje beroende lokalt.

Nästa exempel kod visar hur du initierar `ModelsRepositoryClient` med hjälp av en anpassad bas-URL för databasen, i det här fallet med `raw` URL: er från GitHub-API: et utan att använda `expanded` formuläret eftersom den inte är tillgänglig i `raw` slut punkten. `AzureEventSourceListener`Initieras för att kontrol lera HTTP-begäran som utförs av klienten:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Det finns fler tillgängliga exempel i käll koden i Azure SDK GitHub-databasen: [Azure. IoT. ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Publicera en modell

> [!Important]
> Du måste ha ett GitHub-konto för att kunna skicka modeller till den offentliga DMR.

1. Förgrena den offentliga GitHub-databasen: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Klona den förgrenade lagrings platsen. Du kan också skapa en ny gren för att hålla dina ändringar isolerade från `main` grenen.
1. Lägg till de nya gränssnitten i `dtmi` mappen med hjälp av katalogen/fil namns konventionen. Mer information finns i [Importera en modell till `dtmi/` mappen](#import-a-model-to-the-dtmi-folder).
1. Validera modellerna lokalt med `dmr-client` verktyget. Mer information finns i [validera modeller](#validate-models).
1. Genomför ändringarna lokalt och skicka dem till din förgrening.
1. Från din förgrening skapar du en pull-begäran som är riktad mot `main` grenen. Se [skapa ett ärende-eller pull-begäranden](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Granska [kraven för pull-begäran](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Pull-begäran utlöser en uppsättning GitHub-åtgärder som validerar de inskickade gränssnitten och ser till att pull-begäran uppfyller alla krav.

Microsoft kommer att svara på en pull-begäran med alla kontroller inom tre arbets dagar.

### <a name="dmr-client-tools"></a>`dmr-client` formateringsverktyg

De verktyg som används för att validera modeller under PR-kontrollerna kan också användas för att lägga till och validera DTDL-gränssnitten lokalt.

> [!NOTE]
> Det här verktyget kräver [.NET SDK](https://dotnet.microsoft.com/download) version 3,1 eller senare.

### <a name="install-dmr-client"></a>Installationer `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importera en modell till `dtmi/` mappen

Om din modell redan är lagrad i JSON-filer kan du använda `dmr-client import` kommandot för att lägga till dem i `dtmi/` mappen med rätt fil namn:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> Du kan använda `--local-repo` argumentet för att ange den lokala rot katalogen.

### <a name="validate-models"></a>Validera modeller

Du kan validera dina modeller med `dmr-client validate` kommandot:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> Verifieringen använder den senaste DTDL parser-versionen för att säkerställa att alla gränssnitt är kompatibla med språk specifikationen DTDL.

För att verifiera externa beroenden måste de finnas i den lokala lagrings platsen. Om du vill validera modeller använder du `--repo` alternativet för att ange en- `local` eller- `remote` mapp för att matcha beroenden:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Strikt verifiering

DMR innehåller ytterligare [krav](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md), Använd- `stict` flaggan för att validera din modell mot dem:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Kontrol lera konsolens utdata för eventuella fel meddelanden.

### <a name="export-models"></a>Exportera modeller

Modeller kan exporteras från en viss lagrings plats (lokal eller fjärr) till en enda fil med en JSON-matris:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att granska [IoT plug and Play-arkitekturen](concepts-architecture.md).
