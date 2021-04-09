---
title: Konfigurera moln mått och loggar för Azure API Management egen värd-Gateway | Microsoft Docs
description: Lär dig hur du konfigurerar moln mått och loggar för Azure API Management egen värd-Gateway
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574063"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurera moln mått och loggar för Azure API Management egen värd-Gateway

Den här artikeln innehåller information om hur du konfigurerar moln mått och loggar för den [egna värdbaserade gatewayen](./self-hosted-gateway-overview.md).

Den lokala gatewayen måste associeras med en API Management-tjänst och kräver utgående TCP/IP-anslutning till Azure på port 443. Gatewayen utnyttjar den utgående anslutningen för att skicka telemetri till Azure, om den är konfigurerad att göra det. 

## <a name="metrics"></a>Mått
Som standard avger gatewayen för egen värd ett antal mått genom [Azure Monitor](https://azure.microsoft.com/services/monitor/), samma som den hanterade gatewayen [i molnet](api-management-howto-use-azure-monitor.md). 

Funktionen kan aktive ras eller inaktive ras med `telemetry.metrics.cloud` nyckeln i ConfigMap för gateway-distributionen. Nedan visas en uppdelning av de tillgängliga konfigurationerna:

| Fält  | Standardvärde | Beskrivning |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Aktiverar loggning via Azure Monitor. Värdet kan vara `true` , `false` . |


Här är en exempel konfiguration:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Den egen värdbaserade gatewayen avger för närvarande följande mått genom Azure Monitor:

| Metric  | Beskrivning |
| ------------- | ------------- |
| Begäranden  | Antal API-begäranden under perioden |
| Varaktighet för gateway-begäranden | Antalet millisekunder från att gatewayen fick begäran till då svaret har skickats fullständigt |
| Varaktighet för backend-begäranden | Antalet millisekunder som ägnats åt övergripande serverdels-IO (ansluta, skicka och ta emot byte)  |

## <a name="logs"></a>Loggar

Den egen värdbaserade gatewayen skickar för närvarande inte [diagnostikloggar](./api-management-howto-use-azure-monitor.md#activity-logs) till molnet. Det är dock möjligt att [Konfigurera och Spara loggar lokalt](how-to-configure-local-metrics-logs.md) där gatewayen för egen värd distribueras. 

Om en gateway distribueras i [Azure Kubernetes-tjänsten](https://azure.microsoft.com/services/kubernetes-service/)kan du aktivera [Azure Monitor för behållare](../azure-monitor/containers/container-insights-overview.md) för att samla in loggar från dina behållare och visa dem i Log Analytics. 


## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Lär dig mer om att [Konfigurera och Spara loggar lokalt](how-to-configure-local-metrics-logs.md)
