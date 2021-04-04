---
title: Hantera dina SOC bättre med incident mått i Azure Sentinel | Microsoft Docs
description: Använd information från skärmen för Azure Sentinel-incidentens mått och arbets bok för att hjälpa dig att hantera säkerhets åtgärds Center (SOC).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660737"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Hantera SOC bättre med incidentmått

> [!IMPORTANT]
> Funktionerna för incident mått finns för närvarande i offentlig för hands version.
> Dessa funktioner tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Som SOC-ansvarig (Security Operations Center) måste du ha övergripande effektivitets mått och mått för att kunna mäta teamets prestanda. Du ska kunna se incident åtgärder över tid med många olika villkor, till exempel allvarlighets grad, MITRE taktiker, Mean Time to prioritering, Mean Time to resolve och more. Azure Sentinel gör nu dessa data tillgängliga för dig med den nya **säkerhets incident** -tabellen och-schemat i Log Analytics och de tillhör ande arbets böckerna för **säkerhets åtgärder** . Du kan visualisera teamets prestanda över tid och använda den här insikten för att förbättra effektiviteten. Du kan också skriva och använda egna KQL-frågor mot incident tabellen för att skapa anpassade arbets böcker som passar dina specifika gransknings behov och KPI: er.

## <a name="use-the-security-incidents-table"></a>Använda tabellen säkerhets incidenter

**Säkerhets incident** -tabellen är inbyggd i Azure Sentinel. Du hittar den med de andra tabellerna i **SecurityInsights** -samlingen under **loggar**. Du kan fråga den precis som andra tabeller i Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Säkerhets incident tabell":::

Varje gång du skapar eller uppdaterar en incident läggs en ny logg post till i tabellen. Detta gör att du kan spåra ändringar som gjorts i incidenter och tillåter ännu mer kraftfulla SOC-mått, men du måste mindful detta när du skapar frågor för den här tabellen eftersom du kan behöva ta bort dubblettposter för en incident (beroende på vilken fråga du kör). 

Om du till exempel vill returnera en lista över alla incidenter sorterade efter deras incident nummer men bara ville returnera den senaste loggen per incident, kan du göra detta med hjälp av KQL [sammanfattnings operatorn](/azure/data-explorer/kusto/query/summarizeoperator) med `arg_max()` [agg regerings funktionen](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Fler exempel frågor

Genomsnittlig tid för att stänga:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Genomsnittlig tid till prioritering:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Arbets bok för säkerhets åtgärder

För att komplettera **SecurityIncidents** -tabellen har vi tillhandahållit dig en välkomst mall för arbets böcker för **säkerhets åtgärder** som du kan använda för att övervaka dina SOC-åtgärder. Arbets boken innehåller följande mått: 
- Incident skapad över tid 
- Incidenter som skapats genom att stänga klassificering, allvarlighets grad, ägare och status 
- Genomsnittlig tid till prioritering 
- Genomsnittlig tid för att stänga 
- Incidenter som skapats av allvarlighets grad, ägare, status, produkt och taktiker över tid 
- Tid till prioritering percentiler 
- Tid för att stänga percentiler 
- Genomsnittlig tid till prioritering per ägare 
- Senaste aktiviteter 
- Senaste stängnings klassificeringar  

Du hittar den här nya arbets boks mal len genom att välja **arbets böcker** på navigerings menyn i Azure Sentinel och välja fliken **mallar** . Välj **effektivitet för säkerhets åtgärder** i galleriet och klicka på någon av knapparna **Visa sparade arbets böcker** och **Visa mallar** .

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Arbets boks Galleri för säkerhets incidenter":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Arbets boken säkerhets incidenter slutförd":::

Du kan använda mallen för att skapa egna anpassade arbets böcker som är anpassade efter dina specifika behov.

## <a name="securityincidents-schema"></a>SecurityIncidents-schema

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).