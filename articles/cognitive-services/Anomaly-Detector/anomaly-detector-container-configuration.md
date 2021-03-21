---
title: Så här konfigurerar du en behållare för avvikelse detektor API
titleSuffix: Azure Cognitive Services
description: Körnings miljön för avvikelse detektorns API-behållare konfigureras med hjälp av `docker run` kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363722"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurera avvikelseidentifieringscontainrar

Körnings miljön för **avvikelse detektor** behållare konfigureras med hjälp av `docker run` kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurations inställningar:

|Obligatorisk|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Används för att spåra fakturerings information.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Gör att du kan lägga till stöd för [Azure Application Insights](/azure/application-insights) -telemetri till din behållare.|
|Ja|[Billing](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Fluent](#fluentd-settings)|Skriv logg och, om du vill, Metric-data till en Fluent-Server.|
|Inga|[Http-proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begär Anden.|
|Inga|[Loggning](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Inga|[Monterar](#mount-settings)|Läs och Skriv data från värddatorn till behållare och från behållare tillbaka till värddatorn.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna, [`Billing`](#billing-configuration-setting) och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla dessa tre. i annat fall startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

`ApiKey`Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _avvikelse detektor_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure Portal: **avvikelse detektorns** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

`Billing`Inställningen anger slut punkts-URI för _avvikelse identifierings_ resursen på Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _avvikelse detektor_ resurs på Azure.

Du hittar den här inställningen på följande plats:

* Azure Portal: **avvikelse detektorns** översikt, märkt `Endpoint`

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](anomaly-detector-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för http-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom `--mount` att ange alternativet i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

De avvikande detektor behållarna använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](anomaly-detector-container-howto.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Avvikelse detektor behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon 

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run` kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](anomaly-detector-container-howto.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck, `\` som ett linje fortsättnings alternativ för ett bash-gränssnitt. Ersätt eller ta bort detta baserat på värd operativ systemets krav. Linje fortsättnings tecknet för Windows är till exempel ett cirkumflex, `^` . Ersätt omvänt snedstreck med cirkumflex. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt värdet inom hakparenteser, `{}` med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Resursens slut punkts nyckel `Anomaly Detector` på sidan med Azure- `Anomaly Detector` nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan Azure- `Anomaly Detector` Översikt.| Se [samla in obligatoriska parametrar](anomaly-detector-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`Alternativen, `Billing` och `ApiKey` måste anges för att köra behållaren, annars startar inte behållaren.  Mer information finns i [fakturering](anomaly-detector-container-howto.md#billing).
> ApiKey-värdet är **nyckeln** från sidan med resurs nycklar för Azure avvikelser. 

## <a name="anomaly-detector-container-docker-examples"></a>Docker-exempel för avvikande detektor behållare

Följande Docker-exempel är för behållaren för avvikelse detektor. 

### <a name="basic-example"></a>Basic-exempel 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Logga exempel med kommando rads argument

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en avvikelse detektor behållare till Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Läs mer om API-tjänsten för avvikelse detektor](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)