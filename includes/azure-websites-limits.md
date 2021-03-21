---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: 80d295d017b11d86df7a3fe4c14afc7a5665cd96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612141"
---
| Resurs | Kostnadsfri | Delad | Basic | Standard | Premium (v1-v3) | Isolerad </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webb-, mobil-eller API-appar](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup>|
| [App Service-plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resurs grupp |100 per resurs grupp |100 per resurs grupp |100 per resurs grupp |100 per resurs grupp|
| Beräknings instans typ |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |Dedikerad<sup>3</sup>|
| [Skala ut](../articles/app-service/manage-scale-up.md) (maximalt antal instanser) |1 delad |1 delad |3 dedikerad<sup>3</sup> |10 dedikerat<sup>3</sup> | 20 dedikerat för v1 och v2; 30 dedikerat för v3. <sup>3</sup>|100 dedikerat<sup>4</sup>|
| Lagring<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Skicka en support förfrågan för mer än 250 GB. |1 TB<sup>5</sup> <br/><br/> Den tillgängliga lagrings kvoten är 999 GB. |
| PROCESSOR tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Minne (1 timme) |1 024 MB per App Service plan |1 024 MB per app |Saknas |Saknas |Saknas |Saknas |
| Bandbredd |165 MB |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |
| Programmets arkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Web Sockets per instans<sup>7</sup> |5 |35 |350 |Obegränsat |Obegränsat |Obegränsat |
| Utgående IP-anslutningar per instans | 600 | 600 | Är beroende av instans storlek<sup>8</sup> | Är beroende av instans storlek<sup>8</sup> | Är beroende av instans storlek<sup>8</sup> | 16 000 |
| Samtidiga [fel söknings anslutningar](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per program |1 |1 |1 |5 |5 |5 |
| App Service certifikat per prenumeration<sup>9</sup>| Stöds inte | Stöds inte |10 |10 |10 |10 |
| Anpassade domäner per app</a> |0 (endast azurewebsites.net-underdomän)|500 |500 |500 |500 |500 |
| Stöd för anpassad domän- [SSL](../articles/app-service/configure-ssl-certificate.md) |Jokertecken för \* . azurewebsites.net är inte stöds som standard|Jokertecken för \* . azurewebsites.net är inte stöds som standard|Obegränsade SNI SSL anslutningar |Obegränsade SNI SSL och 1 IP SSL anslutningar ingår |Obegränsade SNI SSL och 1 IP SSL anslutningar ingår | Obegränsade SNI SSL och 1 IP SSL anslutningar ingår|
| Hybridanslutningar | | | 5 per plan | 25 per plan | 200 per app | 200 per app |
| [Integrering med virtuellt nätverk](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Privata slut punkter](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 per app  |    |
| Integrerad belastningsutjämnare | |X |X |X |X |X<sup>10</sup> |
| [Åtkomst begränsningar](../articles/app-service/networking-features.md#access-restrictions) | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app |
| [Always on](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Schemalagda säkerhets kopieringar](../articles/app-service/manage-backup.md) | | | | Schemalagda säkerhets kopieringar varje 2 timme, högst 12 säkerhets kopior per dag (manuellt + schemalagd) | Schemalagda säkerhets kopieringar varje timme, högst 50 säkerhets kopior per dag (manuellt + schemalagd) | Schemalagda säkerhets kopieringar varje timme, högst 50 säkerhets kopior per dag (manuellt + schemalagd) |
| [Automatisk skalning](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Mellanlagrings platser](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| [Testing in Production](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnostikloggar](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Autentisering och auktorisering](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service hanterade certifikat (offentlig för hands version)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|

<sup>1</sup> appar och lagrings kvoter per app service plan om inget annat anges.

<sup>2</sup> det faktiska antalet appar som du kan vara värd för på de här datorerna beror på aktivitetens appar, storleken på dator instanserna och motsvarande resursutnyttjande.

<sup>3</sup> dedikerade instanser kan ha olika storlekar. Mer information finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).

<sup>4</sup> fler tillåts på begäran.

<sup>5</sup> lagrings gränsen är den totala innehålls storleken för alla appar i samma app service-plan. Den totala innehålls storleken för alla appar i alla App Service-planer i en enda resurs grupp och region får inte överstiga 500 GB. Fil system kvoten för App Service värdbaserade appar bestäms av mängden App Service planer som skapats i en region och resurs grupp.

<sup>6</sup> dessa resurser begränsas av fysiska resurser på dedikerade instanser (instans storlek och antal instanser).

<sup>7</sup> om du skalar en app på Basic-nivån till två instanser har du 350 samtidiga anslutningar för var och en av de två instanserna. För standard-nivån och ovan finns det inga teoretiska gränser för Web Sockets, men andra faktorer kan begränsa antalet Web Sockets. Till exempel tillåts maximalt antal samtidiga begär Anden (definieras av `maxConcurrentRequestsPerCpu` ): 7 500 per liten virtuell dator, 15 000 per virtuell dator (7 500 x 2 kärnor) och 75 000 per stor virtuell dator (18 750 x 4 kärnor).

<sup>8</sup> högsta antal IP-anslutningar är per instans och är beroende av instans storleken: 1 920 per B1/S1/P1V3-instans, 3 968 per B2/S2/P2V3-instans, 8 064 per B3/S3/P3V3-instans.

<sup>9</sup> gränsen för App Service Certificate kvot per prenumeration kan ökas via en support förfrågan till max gränsen på 200.

<sup>10</sup> App Service isolerad SKU: er kan vara internt BELASTNINGSUTJÄMNAD (ILB) med Azure Load Balancer, så det finns ingen offentlig anslutning från Internet. Därför måste vissa funktioner i en ILB isolerad App Service användas från datorer som har direkt åtkomst till ILB-nätverks slut punkten.

<sup>11</sup> kör anpassade körbara filer och/eller skript på begäran, enligt schema eller kontinuerligt som bakgrunds aktivitet i App Service-instansen. Always on krävs för kontinuerliga WebJobs-körningar. Det finns ingen fördefinierad gräns för antalet WebJobs som kan köras i en App Service instans. Det finns praktiska gränser som beror på vad program koden försöker göra.

<sup>12</sup> blott-domäner stöds inte. Endast utfärdande av standard certifikat (jokertecken är inte tillgängliga). Begränsat till endast ett kostnads fritt certifikat per anpassad domän.
