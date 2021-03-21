---
title: Förstå din resurshälsa
titleSuffix: Azure Digital Twins
description: Se hur du använder Azure Resource Health för att kontrol lera hälso tillståndet för din Azure Digital-instansen.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616557"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Felsöka Azure Digitals sammanflätade: resurs hälsa

[Azure Resource Health](../service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support för tjänst problem som påverkar dina Azure-resurser. Den rapporterar om resursernas aktuella och tidigare hälso tillstånd.

Den här artikeln visar hur du hämtar information om **resurs hälsa** för dina Azures digitala dubbla instanser.

## <a name="use-azure-resource-health"></a>Använd Azure Resource Health

Azure Resource Health kan hjälpa dig att övervaka om din Azure Digital-instansen är igång och körs. Du kan också använda den för att lära dig om ett regionalt avbrott påverkar instansens hälsa.

Följ dessa steg om du vill kontrol lera hälso tillståndet för din instans:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Från instansens meny väljer du _**resurs hälsa**_ under *support + fel sökning*. Det tar dig till sidan för att Visa resurs hälso historik. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Skärm bild som visar sidan &quot;resurs hälsa&quot;. I avsnittet hälso historik visas en daglig rapport från de senaste nio dagarna. Varje dag visas statusen tillgänglig.":::

I bilden ovan visas den här instansen som *tillgänglig* och har varit under de senaste nio dagarna. Mer information om *tillgänglig* status och andra status typer som kan visas finns i [*Översikt över Azure Resource Health*](../service-health/resource-health-overview.md).

Du kan också lära dig mer om de olika kontrollerna som ingår i resurs hälsan för olika typer av Azure-resurser i [*resurs typer och hälso kontroller i Azure Resource Health*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Nästa steg

Läs om andra sätt att övervaka din Azure Digital-instansen i följande artiklar:
* [*Fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md)
* [*Fel sökning: Konfigurera diagnostik*](troubleshoot-diagnostics.md).
* [*Fel sökning: Konfigurera aviseringar*](troubleshoot-alerts.md)
