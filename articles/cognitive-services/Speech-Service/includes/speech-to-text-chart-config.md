---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Information om konfigurations alternativen för tal-till-text-Helm.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002309"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Tal till text (under diagram: diagram/speechToText)

Om du vill åsidosätta diagrammet "paraply" lägger du till prefixet `speechToText.` på valfri parameter för att göra det mer exakt. Den kommer till exempel att åsidosätta motsvarande parameter till exempel åsidosättningar `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest` .

|Parameter|Beskrivning|Standardvärde|
| -- | -- | -- |
| `enabled` | Om tjänsten för **tal till text** är aktive rad. | `false` |
| `numberOfConcurrentRequest` | Antalet samtidiga förfrågningar för tjänsten **tal till text** . Det här diagrammet beräknar automatiskt processor-och minnes resurser baserat på det här värdet. | `2` |
| `optimizeForAudioFile`| Om tjänsten behöver optimeras för ljud inspelning via ljudfiler. Om `true` det här diagrammet kommer att allokera mer processor resurser till tjänsten. | `false` |
| `image.registry`| Registrerings avbildningen **tal till text** i Docker. | `containerpreview.azurecr.io` |
| `image.repository` | Bild lagrings platsen för **tal-till-text-** Docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Avbildnings tag gen till Docker för **tal till text** . | `latest` |
| `image.pullSecrets` | Bild hemligheterna för att hämta Docker-avbildningen från **tal till text** . | |
| `image.pullByHash`| Anger om Docker-avbildningen hämtas via hash. Om `true` , `image.hash` krävs. | `false` |
| `image.hash`| Bild **-hashen tal-till-text** Docker. Används endast när `image.pullByHash: true` .  | |
| `image.args.eula` kunna | Visar att du har accepterat licensen. Det enda giltiga värdet är `accept` | |
| `image.args.billing` kunna | URI-värdet för fakturerings slut punkten är tillgängligt på sidan med tal översikts sidan för Azure Portal. | |
| `image.args.apikey` kunna | Används för att spåra fakturerings information. ||
| `service.type` | Kubernetes tjänst typ för tjänsten **tal till text** . Se [anvisningarna för Kubernetes-tjänst typer](https://kubernetes.io/docs/concepts/services-networking/service/) för mer information och kontrol lera stöd för moln leverantörer. | `LoadBalancer` |
| `service.port`|  Porten för tjänsten **tal-till-text** . | `80` |
| `service.annotations` | **Tal-till-text-** anteckningar för tjänste-metadata. Anteckningar är nyckel värdes par. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Om den [automatiska skalnings funktionen för horisontella Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktive rad. Om `true` `speech-to-text-autoscaler` kommer att distribueras i Kubernetes-klustret. | `true` |
| `service.podDisruption.enabled` | Om [Pod störnings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktive rad. Om `true` `speech-to-text-poddisruptionbudget` kommer att distribueras i Kubernetes-klustret. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Sentiment-analys (under diagram: diagram/speechToText)

Från och med v-2.2.0 av tal-till-text-behållaren och v-0.2.0 i Helm-diagrammet används följande parametrar för sentiment analys med hjälp av API för textanalys.

|Parameter|Beskrivning|Värden|Standardvärde|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Om tjänsten för **text analys** är aktive rad| True/false| `false`|
|`textanalytics.image.registry`| Den **text Analytics-** Docker avbildnings register| giltigt Docker-avbildnings register| |
|`textanalytics.image.repository`| **Text Analytics** Docker-avbildningens lagrings plats| giltig Docker-avbildnings lagrings plats| |
|`textanalytics.image.tag`| Bild tag gen för **text Analytics-** Docker| giltig Docker-avbildnings tag| |
|`textanalytics.image.pullSecrets`| Bild hemligheterna för att hämta Docker-avbildningen av **text Analytics**| giltigt namn på hemligheter| |
|`textanalytics.image.pullByHash`| Anger om du ska hämta Docker-avbildningen med hash.  Om `yes` , `image.hash` krävs också. Om `no` anger du det som "falskt". Standardvärdet är `false`.| True/false| `false`|
|`textanalytics.image.hash`| **Text Analytics** Docker-avbildningens hash. Använd den bara med `image.pullByHash:true` .| giltig Docker-avbildnings-hash | |
|`textanalytics.image.args.eula`| Ett av de argument som krävs av en **text analys** behållare, som anger att du har accepterat licensen. Värdet för det här alternativet måste vara: `accept` .| `accept`, om du vill använda behållaren | |
|`textanalytics.image.args.billing`| Ett av de argument som krävs av en **text analys** behållare, som anger URI för fakturerings slut punkt. URI-värdet för fakturerings slut punkten är tillgängligt på sidan med tal översikts sidan för Azure Portal.|giltig URI för fakturerings slut punkt||
|`textanalytics.image.args.apikey`| Ett av de argument som krävs av en **text analys** behållare, som används för att spåra fakturerings information.| giltig apiKey||
|`textanalytics.cpuRequest`| Begärd processor för **text analys** behållare| int| `3000m`|
|`textanalytics.cpuLimit`| Begränsad processor för **text analys** behållare| | `8000m`|
|`textanalytics.memoryRequest`| Begärt minne för **text analys** behållare| | `3Gi`|
|`textanalytics.memoryLimit`| Det begränsade minnet för **text analys** behållare| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| URI-suffixet för sentiment Analysis är hela URI: n i formatet "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Typ av **text analys** tjänst i Kubernetes. Se [Kubernetes-tjänst typer](https://kubernetes.io/docs/concepts/services-networking/service/) | giltig typ av Kubernetes-tjänst | `LoadBalancer` |
|`textanalytics.service.port`| Porten för tjänsten för **text analys**| int| `50085`|
|`textanalytics.service.annotations`| Anteckningarna användare kan lägga till i metadata för **text analys** tjänsten. Till exempel:<br/> **anteckningar**<br/>`   `**vissa/annotation1: värde1**<br/>`  `**vissa/annotation2: värde2** | anteckningar, en per rad| |
|`textanalytics.serivce.autoScaler.enabled`| Om den [automatiska skalnings funktionen för horisontella Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) är aktive rad. Om aktive rad `text-analytics-autoscaler` kommer att distribueras i Kubernetes-klustret | True/false| `true`|
|`textanalytics.service.podDisruption.enabled`| Om [Pod störnings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) är aktive rad. Om aktive rad `text-analytics-poddisruptionbudget` kommer att distribueras i Kubernetes-klustret| True/false| `true`|
