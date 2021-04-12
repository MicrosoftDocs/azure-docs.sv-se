---
title: Konfigurera övervakning och mått med hjälp av Azure Monitor
titleSuffix: Azure Bastion
description: Lär dig mer om Azure skydds Monitoring och mått med Azure Monitor, lösning för mått, varningar, diagnostikloggar i Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: c4e03318fae8d8d3a8b4d29538cad49f9ef39593
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259123"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Konfigurera övervakning och mått för Azure-skydds med hjälp av Azure Monitor

Den här artikeln hjälper dig att arbeta med övervakning och mått för Azure skydds med hjälp av Azure Monitor.

>[!NOTE]
>Det rekommenderas inte att använda **klassiska mått** .
>

## <a name="about-metrics"></a>Om mått

Azure skydds har olika mått som är tillgängliga. I följande tabell visas kategorin och dimensionerna för varje tillgängligt mått.

|**Mått**|**Kategori**|**Dimension (er)**|
| --- | --- | --- |
|Status för skydds-kommunikation * *|[Tillgänglighet](#availability)|Ej tillämpligt|
|Totalt minne|[Tillgänglighet](#availability)|Instans|
|Använd processor|[Trafik](#traffic)|Instans
|Använt minne|[Trafik](#traffic)|Instans
|Antal sessioner|[Prestanda](#performance)|Instans|

* * Skydds kommunikations status gäller bara för skydds-värdar som distribuerats efter 2020 november.

### <a name="availability-metrics"></a><a name="availability"></a>Tillgänglighets mått

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Status för skydds-kommunikation

Du kan visa kommunikations statusen för Azure-skydds, som sammanställs över alla instanser som omfattar skydds-värden.

* Värdet **1** anger att skydds är tillgängligt.
* Värdet **0** anger att skydds-tjänsten inte är tillgänglig.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Skärm bild som visar kommunikations status.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Totalt minne

Du kan visa det totala minnet för Azure-skydds, delat över varje skydds-instans.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Skärm bild som visar totalt minne.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Trafik mått

#### <a name="used-cpu"></a><a name="used-cpu"></a>Använd processor

Du kan visa CPU-användningen för Azure-skydds, dela över varje skydds-instans. Övervakning av det här måttet hjälper dig att mäta tillgänglighet och kapacitet för de instanser som utgör Azure-skydds

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Skärm bild som visar CPU som används.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Använt minne

Du kan visa minnes användningen för varje skydds-instans genom att dela över varje skydds-instans. Övervakning av det här måttet hjälper dig att mäta tillgänglighet och kapacitet för de instanser som utgör Azure-skydds.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Skärm bild som visar använt minne.":::

### <a name="performance-metrics"></a><a name="performance"></a>Prestanda mått

#### <a name="session-count"></a>Antal sessioner

Du kan visa antalet aktiva sessioner per skydds-instans som sammanställs över varje typ av session (RDP och SSH). Varje Azure-skydds har stöd för en mängd aktiva RDP-och SSH-sessioner. Genom att övervaka det här måttet får du hjälp att förstå om du behöver justera antalet instanser som kör skydds-tjänsten. Mer information om antalet sessioner som Azure skydds kan stödja finns i [vanliga frågor och svar om Azure skydds](bastion-faq.md).

De rekommenderade värdena för den här mått konfigurationen är:

* **Sammansättning:** Gmsn
* **Kornig het:** 5 eller 15 minuter
* Delning efter instanser rekommenderas för att få ett mer korrekt antal

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Skärm bild som visar antalet sessioner.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Visa mått

1. Om du vill visa mått navigerar du till din skydds-värd.
1. Välj **mått** i listan **övervakning** .
1. Välj parametrarna. Om inga mått har angetts klickar du på **Lägg till mått** och väljer sedan parametrarna.

   * **Omfattning:** Som standard är omfånget inställt på skydds-värden.
   * **Mått namn område:** Standard mått.
   * **Mått:** Välj det mått som du vill visa.

1. När ett mått har valts tillämpas standard agg regeringen. Du kan också använda dela upp som visar måttet med olika dimensioner.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
  
