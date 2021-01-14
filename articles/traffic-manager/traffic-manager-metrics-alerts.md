---
title: Mått och aviseringar i Azure Traffic Manager
description: I den här artikeln lär du dig vilka mått och aviseringar som är tillgängliga för Traffic Manager i Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: bd3588c207fe8828744a665539830d81805bd7b3
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184279"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager mått och aviseringar

Traffic Manager ger dig DNS-baserad belastnings utjämning som innehåller flera metoder för Routning och alternativ för slut punkts övervakning. I den här artikeln beskrivs mått och tillhör ande aviseringar som är tillgängliga för kunder. 

## <a name="metrics-available-in-traffic-manager"></a>Mått som är tillgängliga i Traffic Manager 

Traffic Manager tillhandahåller följande mått per profil som kunder kan använda för att förstå användningen av Traffic Manager och statusen för deras slut punkter under profilen.  

### <a name="queries-by-endpoint-returned"></a>Returnerade frågor efter slut punkt
Använd [det här måttet](../azure-monitor/platform/metrics-supported.md) för att visa antalet frågor som en Traffic Manager profil bearbetar under en angiven period. Du kan också visa samma information på en slut punkts nivå som hjälper dig att förstå hur många gånger en slut punkt returnerades i fråge svaren från Traffic Manager.

I följande exempel visar bild 1 alla fråge svar som Traffic Manager profilen returnerar. 

  
![Sammanställd vy för alla frågor](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Bild 1: sammanställd vy med alla frågor*
  
Bild 2 visar samma information, men den delas upp av slut punkter. Därför kan du se volymen av fråge svar där en angiven slut punkt returnerades.

![Traffic Manager mått – delad vy av frågesträng per slut punkt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Bild 2: delad vy med den fråga volym som visas per returnerad slut punkt*

## <a name="endpoint-status-by-endpoint"></a>Slut punkts status efter slut punkt
Använd [det här måttet](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) för att förstå hälso statusen för slut punkterna i profilen. Det tar två värden:
 - Använd **1** om slut punkten är upp.
 - Använd **0** om slut punkten är nere.

Det här måttet kan visas antingen som ett agg regerings värde som representerar status för alla mått (bild 3), eller så kan det delas (se bild 4) för att visa statusen för vissa slut punkter. Om agg regerings nivån är markerad som **genomsnittlig**, är värdet för det här måttet det aritmetiska genomsnittet av status för alla slut punkter. Om en profil exempelvis har två slut punkter och endast en är felfri, har det här måttet värdet **0,50** som visas i bild 3. 


![Traffic Manager mått – sammansatt vy över slut punkts status](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Bild 3: sammansatt vy över slut punkts status mått – "genomsnittlig" agg regering valt*


![Traffic Manager mått – delad vy över slut punkts status](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Bild 4: delad vy över slut punkts status mått*

Du kan använda dessa mått via [Azure Monitor tjänstens](../azure-monitor/platform/metrics-supported.md)portal, [REST API](/rest/api/monitor/), [Azure CLI](/cli/azure/monitor)och [Azure PowerShell](/powershell/module/az.applicationinsights), eller genom avsnittet mått i Traffic Managers Portal upplevelse.

## <a name="alerts-on-traffic-manager-metrics"></a>Varningar om Traffic Manager mått
Förutom att bearbeta och visa mått från Azure Monitor Traffic Manager kan kunderna konfigurera och ta emot aviseringar som är associerade med dessa mått. Du kan välja vilka villkor som måste uppfyllas i dessa mått för att en avisering ska inträffa, hur ofta dessa villkor måste övervakas och hur aviseringarna ska skickas till dig. Mer information finns i [dokumentationen om Azure Monitor-aviseringar](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Monitor-tjänsten](../azure-monitor/platform/metrics-supported.md)
- Lär dig hur du [skapar ett diagram med hjälp av Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)