---
title: Mått och aviseringar – Azure DNS
description: Med den här utbildnings vägen kan du komma igång med Azure DNS mått och aviseringar.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 391109727877544a4e94bae376ecef4d33a13cfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575282"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS mått och aviseringar
Azure DNS är en värd tjänst för DNS-domäner som tillhandahåller namn matchning med hjälp av Microsoft Azure-infrastrukturen. Den här artikeln beskriver mått och aviseringar för den Azure DNS tjänsten.

## <a name="azure-dns-metrics"></a>Azure DNS mått

Azure DNS tillhandahåller mått för kunder så att de kan övervaka vissa aspekter av sina DNS-zoner som finns i tjänsten. Med Azure DNS mått kan du dessutom konfigurera och ta emot aviseringar baserat på villkor i intresse. Måtten tillhandahålls via den [Azure Monitor tjänsten](../azure-monitor/index.yml). Azure DNS tillhandahåller följande mått via Azure Monitor för dina DNS-zoner:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Du kan också se [definitionen av dessa mått](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones) på sidan Azure Monitor dokumentation.
>[!NOTE]
> För närvarande är dessa mått bara tillgängliga för offentliga DNS-zoner som finns i Azure DNS. Om du har privata zoner som finns i Azure DNS, kommer dessa mått inte att tillhandahålla data för dessa zoner. Dessutom stöds endast funktionen mått och avisering i Azures offentliga moln. Stöd för suveräna moln kommer att följa vid ett senare tillfälle. 

Det mest detaljerade elementet som du kan se mått för är en DNS-zon. Du kan för närvarande inte se mått för enskilda resurs poster inom en zon.

### <a name="query-volume"></a>Fråga volym

Volym måttet *Volume* i Azure DNS visar volymen DNS-frågor (fråga trafik) som tas emot av Azure DNS för din DNS-zon. Mått enheten är antal och agg regeringen är summan av alla frågor som tagits emot under en viss tids period. 

Om du vill visa det här måttet väljer du mått (förhands granskning) Explorer-upplevelse från fliken övervaka i Azure Portal. Välj din DNS-zon i list rutan resurs, Välj volym mått för fråga och välj sum som agg regering. Skärm bilden nedan visar ett exempel.  Mer information om Metrics Explorer upplevelse och diagram finns i [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md).

![Fråga volym](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Bild: Azure DNS fråga volym mått*

### <a name="record-set-count"></a>Antal post uppsättningar
Måttet *antal post uppsättningar* visar antalet post uppsättningar i Azure DNS för din DNS-zon. Alla post uppsättningar som definierats i din zon räknas. Mått enheten är count och agg regeringen är maximum för alla post mängder. Om du vill visa det här måttet väljer du **mått (förhands granskning) Explorer-** upplevelse från fliken **övervaka** i Azure Portal. Välj din DNS-zon i list rutan **resurs** , Välj måttet **uppsättning av uppsättning med data uppsättning** och välj sedan **Max** som **agg regering**. Mer information om Metrics Explorer upplevelse och diagram finns i [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md). 

![Antal post uppsättningar](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Bild: mått för Azure DNS post uppsättnings antal*


### <a name="record-set-capacity-utilization"></a>Kapacitets användning för post uppsättning
Måttet för *data uppsättningens kapacitetsutnyttjande* i Azure DNS visar procent andelen för användningen av din Recordset-kapacitet för en DNS-zon. Varje DNS-zon i Azure DNS omfattas av en Recordset-gräns som definierar det maximala antalet post uppsättningar som tillåts för zonen (se [DNS-gränser](dns-zones-records.md#limits)). Därför visar det här måttet hur nära du är när du ska trycka på post mängds gränsen. Om du till exempel har 500-Recordset som kon figurer ATS för din DNS-zon och zonen har standard post mängds gränsen 5000, visar RecordSetCapacityUtilization-måttet värdet 10% (som erhålls genom att dividera 500 med 5000). Mått enheten är i **procent** och **agg regerings** typen är **maximum**. Om du vill visa det här måttet väljer du mått (förhands granskning) Explorer-upplevelse från fliken övervaka i Azure Portal. Välj din DNS-zon i list rutan resurs, Välj mått för kapacitets användning för data uppsättning och välj Max som agg regering. Skärm bilden nedan visar ett exempel. Mer information om Metrics Explorer upplevelse och diagram finns i [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-charts.md). 

![Skärm bild som whows ett exempel på hur du kan visa mått.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Bild: Azure DNS post uppsättnings mått för kapacitets användning*

## <a name="alerts-in-azure-dns"></a>Aviseringar i Azure DNS
Azure Monitor ger möjlighet att varna mot tillgängliga mått värden. DNS-måtten är tillgängliga i den nya aviserings konfigurations upplevelsen. Som beskrivs i detalj i [dokumentationen för Azure Monitor-aviseringar](../azure-monitor/alerts/alerts-metric.md)kan du välja DNS-zon som resurs, välja mått signal typ och konfigurera aviserings logiken och andra parametrar, till exempel **period** och **frekvens**. Du kan ytterligare definiera en [Åtgärds grupp](../azure-monitor/alerts/action-groups.md) för när aviserings villkoret är uppfyllt, genom att ange att aviseringen ska levereras via de valda åtgärderna. Mer information om hur du konfigurerar aviseringar för Azure Monitor mått finns i [skapa, Visa och hantera aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure DNS](dns-overview.md).