---
title: Så här konfigurerar du en behållare för formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar formulärets tolknings behållare för att tolka formulär-och tabell data.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "86538011"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurera formulär igenkännings behållare

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Med hjälp av Azure formulär igenkännings behållare kan du skapa en program arkitektur som är optimerad för att dra nytta av både robusta moln funktioner och Edge-plats.

Du konfigurerar körnings miljön för formulär igenkännings behållare med hjälp av `docker run` kommando argumenten. Den här behållaren har flera nödvändiga inställningar och några valfria inställningar. Några exempel finns i avsnittet ["exempel på Docker körnings kommandon"](#example-docker-run-commands) . De behållar-/regionsspecifika inställningarna är fakturerings inställningarna.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna, [`Billing`](#billing-configuration-setting) och [`Eula`](#eula-setting) används tillsammans. Du måste ange giltiga värden för alla tre inställningarna. annars startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

`ApiKey`Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Värdet för ApiKey måste vara en giltig nyckel för _formulär igenkännings_ resursen som anges `Billing` i avsnittet "konfigurations inställning för fakturering".

Du hittar den här inställningen i Azure Portal i **formulär igenkännings resurs hantering** under **nycklar**.

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

`Billing`Inställningen anger slut punkts-URI för _formulär igenkännings_ resursen på Azure som används för att mäta fakturerings information för behållaren. Värdet för den här konfigurations inställningen måste vara en giltig slut punkts-URI för en _formulär igenkännings_ resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen i rutan Azure Portal, i **formulär igenkännings översikt**, under **slut punkt**.

|Obligatorisk| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](form-recognizer-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller en utmatnings montering genom att ange `--mount` alternativet i [ `docker run` kommandot](https://docs.docker.com/engine/reference/commandline/run/).

Formulär identifierarens behållare kräver en indata montering och en utgående data montering. Monteringen av indata kan vara skrivskyddad och krävs för åtkomst till de data som används för utbildning och bedömning. Monteringen av utdata måste vara skrivbar och du kan använda den för att lagra modeller och temporära data.

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](form-recognizer-container-howto.md#the-host-computer) monterings plats inte är tillgänglig på grund av en konflikt mellan Docker-tjänstens konto behörigheter och värd monterings platsens behörigheter.

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Krävs| `Input` | Sträng | Målet för den inmatade monteringen. Standardvärdet är `/input`.    <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obligatorisk| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`.  <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run` kommandon. När den körs fortsätter behållaren att köras tills du [stoppar den](form-recognizer-container-howto.md#stop-the-container).

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder ett omvänt snedstreck ( \\ ) som ett fortsättnings streck. Ersätt eller ta bort det här tecknen, beroende på värd operativ systemets krav.
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} i följande tabell med dina egna värden:

| Platshållare | Värde |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Den nyckel som används för att starta behållaren. Den finns på sidan Azure Portal formulär igenkännings nycklar. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | URI-värdet för fakturerings slut punkten är tillgängligt på översikts sidan för Azure Portal formulär igenkänning.|
| **{COMPUTER_VISION_API_KEY}** | Nyckeln finns på sidan Azure Portal API för visuellt innehåll nycklar.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Fakturerings slut punkten. Om du använder en molnbaserad Visuellt innehåll resurs finns URI-värdet på översikts sidan för Azure Portal API för visuellt innehåll. Om du använder en *kognitivt-tjänster-identifiera-text* behållare använder du URL: en för fakturerings slut punkt som skickas till behållaren i `docker run` kommandot. |

Se [samla in obligatoriska parametrar](form-recognizer-container-howto.md#gathering-required-parameters) för information om hur du hämtar dessa värden.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Om du vill köra behållaren anger du `Eula` `Billing` alternativen, och `ApiKey` . annars startar inte behållaren. Mer information finns i [fakturering](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Formulär identifierarens Docker-exempel

Följande Docker-exempel är för formulär igenkännings behållaren.

### <a name="basic-example-for-form-recognizer"></a>Basic-exempel för formulär igenkänning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exempel på loggning av formulär tolken

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nästa steg

* Granska [installations-och körnings behållare](form-recognizer-container-howto.md).
