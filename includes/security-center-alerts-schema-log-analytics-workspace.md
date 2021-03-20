---
title: ta med fil
description: ta med fil
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "79538836"
---
### <a name="the-data-model-of-the-schema"></a>Data modellen för schemat

|Fält|Beskrivning|
|----|----|
|**AlertName**|Visnings namn för avisering|
|**AlertType**|unikt aviserings-ID|
|**ConfidenceLevel**|Valfritt Säkerhets nivån för den här aviseringen (hög/låg)|
|**ConfidenceScore**|Valfritt Numerisk förtroende indikator för säkerhets aviseringen|
|**Beskrivning**|Beskrivnings text för aviseringen|
|**DisplayName**|Aviseringens visnings namn|
|**Slut**|Aviseringens effekt slut tid (tidpunkten för den senaste händelsen som bidrar till aviseringen)|
|**Entiteter**|En lista över entiteter som är relaterade till aviseringen. Den här listan kan innehålla en blandning av entiteter av olika typer|
|**ExtendedLinks**|Valfritt En påse för alla länkar som är relaterade till aviseringen. Den här väskan kan innehålla en blandning av länkar för olika typer|
|**ExtendedProperties**|En uppsättning ytterligare fält som är relevanta för aviseringen|
|**IsIncident**|Anger om aviseringen är en incident eller en vanlig avisering. En incident är en säkerhets varning som sammanställer flera aviseringar i en säkerhets incident|
|**ProcessingEndTime**|UTC-tidsstämpeln som aviseringen skapades i|
|**ProductComponentName**|Valfritt Namnet på en komponent inuti produkten som skapade aviseringen.|
|**ProductName**|konstant (' Azure Security Center ')|
|**ProviderName**|outnyttja|
|**RemediationSteps**|Manuella åtgärds objekt att vidta för att åtgärda säkerhetshot|
|**ResourceId**|Fullständig identifierare för den berörda resursen|
|**Allvarlighetsgrad**|Allvarlighets grad för avisering (hög/medel/låg/information)|
|**SourceComputerId**|ett unikt GUID för den berörda servern (om aviseringen genereras på servern)|
|**SourceSystem**|outnyttja|
|**/St**|Aviseringens effekt start tid (tidpunkten för den första händelsen som bidrar till aviseringen)|
|**SystemAlertId**|Unik identifierare för den här säkerhets aviserings instansen|
|**TenantId**|identifieraren för den överordnade Azure Active Directory-klient för den prenumeration som den genomsökta resursen finns i|
|**TimeGenerated**|UTC-tidsstämpeln för vilken utvärderingen ägde rum (Security Center genomsöknings tiden) (identisk med DiscoveredTimeUTC)|
|**Typ**|konstant (' SecurityAlert ')|
|**Namn**|Namnet på leverantören som tillhandahöll aviseringen (t. ex. "Microsoft")|
|**VendorOriginalId**|outnyttja|
|**WorkspaceResourceGroup**|om aviseringen genereras på en virtuell dator, Server, skalnings uppsättning för virtuell dator eller App Service instans som rapporterar till en arbets yta, innehåller resurs grupps namnet för arbets ytan|
|**WorkspaceSubscriptionId**|om aviseringen genereras på en virtuell dator, Server, skalnings uppsättning för virtuell dator eller App Service instans som rapporterar till en arbets yta, innehåller subscriptionId för arbets ytan|
|||
