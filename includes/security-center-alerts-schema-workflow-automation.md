---
title: ta med fil
description: ta med fil
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400970"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Data modellen för schemat

|Fält|Datatyp|Beskrivning|
|----|----|----|
|**AlertDisplayName**|Sträng|Aviseringens visnings namn.|
|**AlertType**|Sträng|Typ av avisering. Aviseringar av samma typ ska ha samma värde. Det här fältet är en nyckelbaserad sträng som representerar typen av avisering och inte en varnings instans. Alla varnings instanser från samma identifierings logik/analys bör ha samma värde för varnings typen.|
|**CompromisedEntity**|Sträng|Visnings namnet för den resurs som är mest relaterad till den här aviseringen.|
|**Beskrivning**|Sträng|Beskrivning av aviseringen.|
|**EndTimeUtc**|DateTime|Tiden för den senaste händelsen eller aktiviteten som ingår i aviseringen.  Fältet måste vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidsinformation.|
|**Entiteter**|IEnumerable (IEntity)|En lista över entiteter som är relaterade till aviseringen. Den här listan kan innehålla en blandning av olika typer av entiteter. Entitetstypen kan vara vilken typ som helst som definieras i avsnittet entiteter. Entiteter som inte finns i listan nedan kan också skickas, men det garanterar inte att de kommer att bearbetas (aviseringen kommer inte att kunna verifieras med nya typer av entiteter).|
|**ExtendedProperties**|Ord lista (sträng, sträng)|Providers kan (valfritt) Inkludera anpassade fält här.|
|**Avsikt**|Enum|Det relaterade syftet med Kill-kedjan bakom aviseringen. En lista över vilka värden som stöds och förklaringar av Azure Security Center som stöds av Kill-kedjeen finns i [avsikter](../articles/security-center/alerts-reference.md#intentions).<br/>Det här fältet kan ha flera värden (avgränsade med kommatecken).|
|**IsIncident**|Bool|Det här fältet avgör om aviseringen är en incident (en sammansatt gruppering av flera aviseringar) eller en enskild avisering. Standardvärdet för fältet är "false" (vilket innebär att det är en enda varning).|
|**ProcessingEndTime**|DateTime|Den tid som aviseringen var tillgänglig för slutanvändaren i den ursprungliga produkt som innehåller aviseringen.|
|**ProductName**|Sträng|Namnet på produkten som publicerade aviseringen (Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS och så vidare).|
|**RemediationSteps**|Lista<String>|Manuella åtgärds objekt att vidta för att åtgärda aviseringen.|
|**ResourceIdentifiers**|Lista (resurs identifierare)|Resurs-ID: n för den här aviseringen som kan användas för att dirigera aviseringen till rätt produkt exponerings grupp (klient organisation, arbets yta, prenumeration osv.). Det kan finnas flera identifierare av olika typer per avisering.|
|**Allvarlighetsgrad**|Enum|Allvarlighets graden för aviseringen som rapporteras av providern. Möjliga värden: information, låg, medel och hög.|
|**StartTimeUtc**|DateTime|Tiden för den första händelsen eller aktiviteten som ingår i aviseringen. Fältet måste vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidsinformation.|
|**Status**|Enum|Aviseringens livs cykel status.<br/>Status som stöds är: ny, löst, avstängd, okänd.<br/>En avisering som anger ett annat värde än de alternativ som stöds tilldelas status okänd.<br/>En avisering som inte anger ett värde tilldelas status New.|
|**SystemAlertId**|Sträng|Aviserings-ID: n.|
|**TimeGenerated**|DateTime|Tiden som aviseringen genererades av aviserings leverantören. Om de inte rapporteras av interna aviserings leverantörer kan en produkt välja att tilldela den tid som den fick för bearbetning av produkten.  Fältet måste vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidsinformation.|
|**Namn**|Sträng|Namnet på den leverantör som aktiverar aviseringen.|
|||
