---
title: Inställningar för Docker-behållare – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS container Runtime Environment konfigureras med hjälp av `docker run` kommando argumenten. LUIS har flera inställningar som krävs, tillsammans med några få valfria inställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001212"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurera Language Understanding Docker-behållare 

**Language Understanding** (Luis) container Runtime Environment konfigureras med hjälp av `docker run` kommando argumenten. LUIS har flera inställningar som krävs, tillsammans med några få valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De maskinvaruspecifika inställningarna är ingångs [monterings inställningar](#mount-settings) och fakturerings inställningar. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurations inställningar:

|Obligatorisk|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Används för att spåra fakturerings information.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Gör att du kan lägga till stöd för [Azure Application Insights](/azure/application-insights) -telemetri till din behållare.|
|Ja|[Billing](#billing-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Fluent](#fluentd-settings)|Skriv logg och, om du vill, Metric-data till en Fluent-Server.|
|Inga|[Http-proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begär Anden.|
|Inga|[Loggning](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Ja|[Monterar](#mount-settings)|Läs och Skriv data från värddatorn till behållare och från behållare tillbaka till värddatorn.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)Inställningarna, [`Billing`](#billing-setting) och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla dessa tre. i annat fall startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey-inställning

`ApiKey`Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-setting) konfigurations inställningen.

Du hittar den här inställningen på följande platser:

* Azure Portal: **Cognitive Services** resurs hantering under **nycklar**
* LUIS Portal: **Inställningar för nycklar och slut punkt** . 

Använd inte start nyckeln eller redigerings nyckeln. 

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Fakturerings inställning

`Billing`Inställningen anger slut punkts-URI för den _Cognitive Services_ resursen på Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande platser:

* Azure Portal: **Cognitive Services** översikt, etiketterad `Endpoint`
* LUIS Portal: **Inställningar för nycklar och slut punkt** som en del av slut punktens URI.

| Obligatorisk | Name | Datatyp | Beskrivning |
|----------|------|-----------|-------------|
| Ja      | `Billing` | sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](luis-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom `--mount` att ange alternativet i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . 

LUIS-behållaren använder inte indata eller utmatnings montering för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](luis-container-howto.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

I följande tabell beskrivs de inställningar som stöds.

|Obligatorisk| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Ja| `Input` | Sträng | Målet för den inmatade monteringen. Standardvärdet är `/input`. Det här är platsen för LUIS. <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Inga| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar LUIS-frågemeddelanden och behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run` kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](luis-container-howto.md#stop-the-container) den.

* I de här exemplen används katalogen från `C:` enheten för att undvika eventuella behörighets konflikter i Windows. Om du behöver använda en speciell katalog som indatalistan kan du behöva ge Docker-tjänstens behörighet. 
* Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.
* Om du använder ett annat operativ system använder du rätt konsol/Terminal, kommandosyntax för montering och linje fortsättnings text för systemet. Dessa exempel förutsätter en Windows-konsol med ett linje fortsättnings steg `^` . Eftersom behållaren är ett Linux-operativsystem använder mål-Mount en syntax för en mappvy i Linux-typ.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Resursens slut punkts nyckel `LUIS` på sidan med Azure- `LUIS` nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan Azure- `LUIS` Översikt.| Se [samla in obligatoriska parametrar](luis-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren. Mer information finns i [fakturering](luis-container-howto.md#billing).
> ApiKey-värdet är **nyckeln** på sidan nycklar och slut punkter i Luis-portalen och finns också på sidan med Azures `Cognitive Services` resurs nycklar. 

### <a name="basic-example"></a>Basic-exempel

I följande exempel finns minsta möjliga argument för att köra behållaren:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights-exempel

I följande exempel anges argumentet ApplicationInsights för att skicka telemetri till Application Insights medan behållaren körs:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Loggnings exempel 

Följande kommando anger loggnings nivån, `Logging:Console:LogLevel` , för att konfigurera loggnings nivån till [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](luis-container-howto.md)
* Se [fel sökning](troubleshooting.md) för att lösa problem som rör Luis-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)