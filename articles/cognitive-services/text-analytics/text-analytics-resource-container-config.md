---
title: Konfigurera behållare – Textanalys
titleSuffix: Azure Cognitive Services
description: Textanalys förser varje behållare med ett gemensamt konfigurations ramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri och säkerhets inställningar för dina behållare.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f6a1bc652125990a7daf3414895f34b95c544912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "83590562"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurera Textanalys Docker-behållare

Textanalys förser varje behållare med ett gemensamt konfigurations ramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri och säkerhets inställningar för dina behållare. Flera [exempel på Docker-körnings kommandon](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run) är också tillgängliga.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna, [`Billing`](#billing-configuration-setting) och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla dessa tre. i annat fall startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

`ApiKey`Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _textanalys_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure Portal: **textanalys** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

`Billing`Inställningen anger slut punkts-URI för den _textanalys_ resursen på Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _ _textanalys_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure Portal: **textanalys** översikt, etiketterad `Endpoint`

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Textanalys behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Textanalys behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](how-tos/text-analytics-how-to-install-containers.md)
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
