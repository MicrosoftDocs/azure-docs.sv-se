---
title: Vanliga frågor om nätverk i Azure Functions
description: Svar på några av de vanligaste frågorna och scenarierna för nätverk med Azure Functions.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592310"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Vanliga frågor om nätverk i Azure Functions

Den här artikeln innehåller vanliga frågor om nätverk i Azure Functions. En mer omfattande översikt finns i [funktions nätverks alternativ](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hur gör jag för att anger du en statisk IP-adress i functions?

Att distribuera en funktion i en App Service-miljön är det primära sättet att ha statiska inkommande och utgående IP-adresser för dina funktioner. Om du vill ha mer information om hur du använder en App Service-miljön börjar du med artikeln [skapa och använder en intern belastningsutjämnare med en app service-miljön](../app-service/environment/create-ilb-ase.md).

Du kan också använda en NAT-gateway för virtuella nätverk för att dirigera utgående trafik via en offentlig IP-adress som du styr. Mer information finns i [Självstudier: kontrol lera Azure Functions utgående IP med en Azure Virtual Network NAT gateway](functions-how-to-use-nat-gateway.md). 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hur gör jag för att begränsa Internet åtkomst till min funktion?

Du kan begränsa Internet åtkomst på ett par olika sätt:

* [IP-begränsningar](../app-service/app-service-ip-restrictions.md): begränsa inkommande trafik till din Function-app via IP-intervall.
    * Under IP-begränsningar kan du också konfigurera [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md), vilket begränsar din funktion till att endast acceptera inkommande trafik från ett visst virtuellt nätverk.
* Borttagning av alla HTTP-utlösare. För vissa program räcker det att bara undvika HTTP-utlösare och använda andra händelse källor för att utlösa din funktion.

Tänk på att Azure Portals redigeraren kräver direkt åtkomst till den funktion som körs. Alla kod ändringar som görs via Azure Portal kräver att den enhet som du använder för att bläddra på portalen ska ha sin IP-adress tillagd i listan över godkända. Men du kan fortfarande använda något på fliken plattforms funktioner med nätverks begränsningar på plats.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hur gör jag för att begränsa min Function-app till ett virtuellt nätverk?

Du kan begränsa **inkommande** trafik för en Function-app till ett virtuellt nätverk med hjälp av [tjänst slut punkter](./functions-networking-options.md#use-service-endpoints). Den här konfigurationen tillåter fortfarande att Function-appen gör utgående anrop till Internet.

För att helt begränsa en funktion så att all trafik flödar genom ett virtuellt nätverk kan du använda en [privat slut punkt](./functions-networking-options.md#private-endpoint-connections) med utgående integrering av virtuella nätverk eller en app service-miljön. Mer information finns i [integrera Azure Functions med ett virtuellt Azure-nätverk med hjälp av privata slut punkter](functions-create-vnet.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hur kan jag få åtkomst till resurser i ett virtuellt nätverk från en Function-app?

Du kan komma åt resurser i ett virtuellt nätverk från en funktion som körs med hjälp av integrering av virtuella nätverk. Mer information finns i [integrering av virtuella nätverk](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hur gör jag för att åtkomst resurser som skyddas av tjänst slut punkter?

Med hjälp av integrering med virtuella nätverk kan du komma åt tjänster-slut punkts säkra resurser från en funktion som körs. Mer information finns i [integrering av virtuella nätverk](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hur kan jag utlösa en funktion från en resurs i ett virtuellt nätverk?

Du kan tillåta att HTTP-utlösare anropas från ett virtuellt nätverk med hjälp av [tjänst slut punkter](./functions-networking-options.md#use-service-endpoints) eller [anslutningar till privata slut punkter](./functions-networking-options.md#private-endpoint-connections). 

Du kan också utlösa en funktion från alla andra resurser i ett virtuellt nätverk genom att distribuera din Function-app till en Premium plan, App Service plan eller App Service-miljön. Mer information finns i [virtuella nätverks utlösare som inte är http](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hur kan jag distribuera min Function-app i ett virtuellt nätverk?

Att distribuera till en App Service-miljön är det enda sättet att skapa en Function-app som är helt inne i ett virtuellt nätverk. Om du vill ha mer information om hur du använder en intern belastningsutjämnare med en App Service-miljön börjar du med artikeln [skapa och använder en intern belastningsutjämnare med en app service-miljön](../app-service/environment/create-ilb-ase.md).

I scenarier där du behöver endast enkelriktad åtkomst till virtuella nätverks resurser eller mindre omfattande nätverks isolering, se [Översikt över funktioner i nätverket](functions-networking-options.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nätverk och funktioner: 

* [Följ självstudien om att komma igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs mer om nätverks alternativen i Azure Functions](./functions-networking-options.md)
* [Lär dig mer om virtuell nätverks integrering med App Service och funktioner](../app-service/web-sites-integrate-with-vnet.md)
* [Lär dig mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll funktioner med App Service miljöer](../app-service/environment/intro.md)
