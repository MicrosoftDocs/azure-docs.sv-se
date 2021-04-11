---
title: Snabb start – introduktion till exempel appen – Azure våren Cloud
description: Beskriver exempel programmet som används i den här serien med snabb starter för distribution till Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879329"
---
# <a name="introduction-to-the-sample-app"></a>Introduktion till exempelappen

::: zone pivot="programming-language-csharp"
Den här serien med snabb starter använder en exempel-app som består av två mikrotjänster för att visa hur du distribuerar en .NET Core Steeltoe-app till moln tjänsten Azure våren. Du kommer att använda Azure våren Cloud-funktioner som tjänst identifiering, konfigurations Server, loggar, mått och distribuerad spårning.

## <a name="functional-services"></a>Funktionella tjänster

Exempel appen består av två mikrotjänster:

* `planet-weather-provider`Tjänsten returnerar väder text som svar på en HTTP-begäran som anger namn på planet. Den kan till exempel returnera "mycket varm" för planeten kvicksilver. Den hämtar väder data från konfigurations servern. Konfigurations servern hämtar väder data från en YAML-fil i en git-lagringsplats, till exempel:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather`Tjänsten returnerar data för fyra planet som svar på en HTTP-begäran. Den hämtar data genom att göra fyra HTTP-begäranden till `planet-weather-provider` . Den använder tjänsten Eureka Server Discovery för att anropa `planet-weather-provider` . Den returnerar JSON, till exempel:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Följande diagram illustrerar exempel arkitekturen för appar:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Exempel på App-diagram":::

## <a name="code-repository"></a>Kodlagringsplats

Exempel appen finns i mappen [steeltoe-Sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) i databasen Azure-samples/Azure-våren-Cloud-samples på GitHub.

Anvisningarna i följande snabb starter hänvisar till käll koden vid behov.

::: zone-end

::: zone pivot="programming-language-java"
I den här snabb starten använder vi ett exempel på en personlig finans som heter PiggyMetrics för att visa hur du distribuerar en app till moln tjänsten Azure våren. PiggyMetrics demonstrerar mönstret för mikrotjänstens arkitektur och fokuserar på tjänsternas uppdelning. Du kommer att se hur det distribueras till Azure med kraftfulla moln funktioner för Azure våren, inklusive tjänst identifiering, konfigurations Server, loggar, mått och distribuerad spårning.

Om du vill följa moln distributions exemplen för Azure våren behöver du bara platsen för käll koden, som tillhandahålls vid behov.

## <a name="functional-services"></a>Funktionella tjänster

PiggyMetrics delas upp i tre Core-mikrotjänster. Alla är oberoende distributions bara program som organiseras av företags domäner.

* **Konto tjänst (som ska distribueras)**: innehåller allmän logik för användar indata och validering: indata/utgifter, besparingar och konto inställningar.
* **Statistik tjänst (används inte i den här snabb starten)**: utför beräkningar på viktiga statistik parametrar och fångar tids serier för varje konto. Datapoint innehåller värden, normaliserad till bas valuta och tids period. Dessa data används för att spåra kassa flödet Dynamics i kontots livs längd.
* **Meddelande tjänst (används inte i den här snabb starten)**: lagrar användarens kontakt uppgifter och meddelande inställningar, till exempel en påminnelse-och säkerhets kopierings frekvens. Schemalagda arbetare samlar in nödvändig information från andra tjänster och skickar e-postmeddelanden till kunder som prenumererar på prenumerationer.

## <a name="infrastructure-services"></a>Infrastruktur tjänster

Det finns flera vanliga mönster i distribuerade system som hjälper till att öka kärn tjänsterna. Azure våren Cloud tillhandahåller kraftfulla verktyg som förbättrar funktionerna för att starta program för att implementera dessa mönster: 

* **Konfigurations tjänst (värd av Azure våren Cloud)**: Azure våren Cloud config är en vågrätt skalbar centraliserad konfigurations tjänst för distribuerade system. Den använder ett lagrings Bart lager som för närvarande stöder lokal lagring, git och under version.
* **Tjänst identifiering (värd av Azure våren Cloud)**: den tillåter automatisk identifiering av nätverks platser för tjänst instanser, vilket kan ha dynamiskt tilldelade adresser på grund av automatisk skalning, problem och uppgraderingar.
* **Auth service (som ska distribueras)** Auktorisations ansvar extraheras fullständigt till en separat server, vilket ger OAuth2-token för Server dels resurs tjänsterna. Auth Server auktoriserar användarautentisering och skyddar maskin-till-dator-kommunikation inuti en perimeter.
* **API-Gateway (distribueras)**: de tre kärn tjänsterna exponerar ett externt API för klienten. I verkliga system kan antalet funktioner växa mycket snabbt med system komplexitet. Hundratals tjänster kan vara inblandade i åter givning av en komplex webb sida. API-gatewayen är en enda start punkt i systemet, som används för att hantera begär Anden och dirigera dem till lämplig server dels tjänst eller för att anropa flera backend-tjänster, vilket aggregerar resultaten. 

## <a name="sample-usage-of-piggymetrics"></a>Exempel på användning av PiggyMetrics

Fullständig implementerings information finns i [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). Exemplen refererar till käll koden vid behov.
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Etablera Azure våren Cloud-instansen](spring-cloud-quickstart-provision-service-instance.md)
