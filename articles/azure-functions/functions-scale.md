---
title: Skala och var värd i Azure Functions
description: Lär dig hur du väljer mellan Azure Functions förbruknings plan och Premium-plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936775"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions värd alternativ

När du skapar en Function-app i Azure måste du välja en värd plan för din app. Det finns tre grundläggande värd planer för Azure Functions: [förbruknings plan](consumption-plan.md), [Premium plan](functions-premium-plan.md)och [dedikerad (App Service) plan](dedicated-plan.md). Alla värd planer är allmänt tillgängliga (GA) på både virtuella Linux-och Windows-datorer.

Värd planen du väljer styr följande beteenden:

* Hur din Function-app skalas.
* De resurser som är tillgängliga för varje funktions program instans.
* Stöd för avancerade funktioner, till exempel Azure Virtual Network-anslutning.

Den här artikeln innehåller en detaljerad jämförelse mellan de olika värd planerna, tillsammans med Kubernetes-baserad värd.

## <a name="overview-of-plans"></a>Översikt över planer

Följande är en sammanfattning av fördelarna med de tre huvudsakliga värd planerna för functions:

| | |
| --- | --- |  
|**[Förbrukningsplan](consumption-plan.md)**| Skala automatiskt och betala bara för beräknings resurser när funktionerna körs.<br/><br/>I förbruknings planen läggs instanser av funktions värden dynamiskt till och tas bort baserat på antalet inkommande händelser.<br/><br/> ✔ Standard värd plan.<br/>✔ Endast betala när funktionerna körs.<br/>✔ Skalas automatiskt, även vid perioder med hög belastning.|  
|**[Premiumplan](functions-premium-plan.md)**|Skalar automatiskt utifrån efter frågan med förvärmade arbetare som kör program utan fördröjning efter att de varit inaktiva, körs på mer kraftfulla instanser och ansluter till virtuella nätverk. <br/><br/>Överväg Azure Functions Premium-planen i följande situationer: <br/><br/>✔ Dina funktions appar körs kontinuerligt eller nästan kontinuerligt.<br/>✔ Du har ett stort antal små körningar och en hög körnings faktura, men med låg GB i förbruknings planen.<br/>✔ Du behöver fler processor-eller minnes alternativ än vad som tillhandahålls av förbruknings planen.<br/>✔ Din kod behöver köra längre än den maximala körnings tiden som tillåts i förbruknings planen.<br/>✔ Du behöver funktioner som inte är tillgängliga i förbruknings planen, till exempel virtuell nätverks anslutning.|  
|**[Dedikerad plan](dedicated-plan.md)** |Kör dina funktioner inom ett App Service plan med jämna [App Service plans taxor](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Bäst för tids krävande scenarier där [Durable Functions](durable/durable-functions-overview.md) inte kan användas. Överväg ett App Service plan i följande situationer:<br/><br/>✔ Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service-instanser.<br/>✔ Du vill tillhandahålla en anpassad avbildning som dina funktioner ska köras på. <br/>✔ Förutsägelse skalning och kostnader måste anges.|  

Jämförelse tabellerna i den här artikeln innehåller också följande värd alternativ, som ger den högsta mängden kontroll och isolering där du kan köra dina funktions program.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service-miljön (ASE) är en App Service funktion som ger en helt isolerad och dedikerad miljö för säker körning av App Service appar i hög skala.<br/><br/>ASE är lämpliga för program arbets belastningar som kräver: <br/><br/>✔ Mycket hög skala.<br/>✔ Fullständig beräknings isolering och säker nätverks åtkomst.<br/>✔ Hög minnes användning.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes tillhandahåller en helt isolerad och dedikerad miljö som körs ovanpå Kubernetes-plattformen.<br/><br/> Kubernetes är lämpligt för program arbets belastningar som kräver: <br/>✔ Anpassade maskin varu krav.<br/>✔ Isolering och säker nätverks åtkomst.<br/>✔ Möjlighet att köra i hybrid miljöer eller miljöer med flera moln.<br/>✔ Att köra tillsammans med befintliga Kubernetes-program och-tjänster.|  

I de återstående tabellerna i den här artikeln jämförs planerna för olika funktioner och beteenden. En kostnads jämförelse mellan dynamiska värd planer (förbrukning och Premium) finns på [sidan Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/). För priser för olika dedikerade plan alternativ, se [sidan App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Operativ system/körning

I följande tabell visas stöd för operativ system och språk körnings stöd för värd planerna.

| | Linux<sup>1</sup><br/>Endast kod | Windows<sup>2</sup><br/>Endast kod | Linux<sup>1, 3</sup><br/>Docker-behållare |
| --- | --- | --- | --- |
| **[Förbrukningsplan](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Inget stöd  |
| **[Premiumplan](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Dedikerad plan](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | saknas | saknas |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux är det enda operativ system som stöds för python runtime-stacken. <br/>
<sup>2</sup> Windows är det enda operativ system som stöds för PowerShell runtime-stacken.<br/>
<sup>3</sup> Linux är det enda operativ system som stöds för Docker-behållare.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Skala

I följande tabell jämförs skalnings beteenden för de olika värd planerna.

| | Skala ut | Maximalt antal instanser |
| --- | --- | --- |
| **[Förbrukningsplan](consumption-plan.md)** | [Händelse driven](event-driven-scaling.md). Skala ut automatiskt, även vid perioder med hög belastning. Azure Functions-infrastrukturen skalar processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden baserat på antalet inkommande utlösare. | 200 |
| **[Premiumplan](functions-premium-plan.md)** | [Händelse driven](event-driven-scaling.md). Skala ut automatiskt, även vid perioder med hög belastning. Azure Functions-infrastrukturen skalar processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden, baserat på antalet händelser som dess funktioner aktive ras på. |100|
| **[Dedikerad plan](dedicated-plan.md)**<sup>1</sup> | Manuell/autoskalning |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | Manuell/autoskalning |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Händelse driven autoskalning för Kubernetes-kluster med [KEDA](https://keda.sh). | Varierar beroende &nbsp; på &nbsp; kluster&nbsp;&nbsp;|

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Kall start beteende

|    |    | 
| -- | -- |
| **[Förbruknings &nbsp; plan](consumption-plan.md)** | Appar kan skalas till noll vid inaktivitet, vilket innebär att vissa begär Anden kan ha ytterligare svars tid vid starten.  Förbruknings planen har vissa optimeringar som hjälper till att minska kallstart tiden, inklusive att dra från förvärmade placeholder funktioner som redan har funktions värden och språk processer som körs. |
| **[Premiumplan](functions-premium-plan.md)** | En permanent varm instans för att undvika kall start. |
| **[Dedikerad plan](dedicated-plan.md)** | När körs i en dedikerad plan kan funktions värden köras kontinuerligt, vilket innebär att kall start inte egentligen är ett problem. |
| **[ASE](dedicated-plan.md)** | När körs i en dedikerad plan kan funktions värden köras kontinuerligt, vilket innebär att kall start inte egentligen är ett problem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Beroende på KEDA konfiguration kan appar konfigureras för att undvika en kall start. Om den har kon figurer ATS för att skalas till noll uppstår en kall start för nya händelser. 

## <a name="service-limits"></a>Tjänstbegränsningar

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Nätverksfunktioner

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Fakturering

| | | 
| --- | --- |
| **[Förbrukningsplan](consumption-plan.md)** | Betala endast för den tid som dina funktioner körs. Fakturering baseras på antalet körningar, körningstid och använt minne. |
| **[Premiumplan](functions-premium-plan.md)** | Premium-planen baseras på antalet kärn sekunder och minne som används för alla nödvändiga och förvärmade instanser. Minst en instans per plan måste alltid vara varm. Den här planen ger den mest förutsägbara prissättningen. |
| **[Dedikerad plan](dedicated-plan.md)* | Du betalar samma för functions-appar i en App Service planera precis som för andra App Service resurser, t. ex. Web Apps.|
| **[App Service-miljön (ASE)](dedicated-plan.md)** | Det finns en fast månads avgift för en ASE som betalar för infrastrukturen och som inte ändras med ASE storlek. Det finns också en kostnad per App Service plan vCPU. Alla appar som har en ASE som värd finns i SKU med isolerad prissättning. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Du betalar endast kostnaderna för ditt Kubernetes-kluster. ingen ytterligare fakturering för functions. Din Function-App körs som en program arbets belastning ovanpå klustret, precis som en vanlig app. |

## <a name="next-steps"></a>Nästa steg

+ [Distributions tekniker i Azure Functions](functions-deployment-technologies.md) 
+ [Utvecklarguide för Azure Functions](functions-reference.md)