---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: dad7799cb5a7579b28847e3968b6b38f1f98298a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327666"
---
| Resurs | Kostnadsfri | Delad | Basic | Standard | Premium (v1-v3) | Isolerad </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webb-, mobil- eller API-appar](https://azure.microsoft.com/services/app-service/) [per Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup>|
| [App Service-plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp|
| Typ av beräkningsinstans |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |Dedikerad<sup>3</sup>|
| [Skala ut](../articles/app-service/manage-scale-up.md) (maximalt antal instanser) |1 delad |1 delad |3 dedikerad<sup>3</sup> |10 dedikerad<sup>3</sup> | 20 dedikerade för v1 och v2; 30 dedikerade för v3. <sup>3</sup>|100 dedikerad<sup>4</sup>|
| Lagring<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> |1 TB<sup>5</sup> <br/><br/> Den tillgängliga lagringskvoten är 999 GB. |
| CPU-tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsat, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsat, betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsat, betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Minne (1 timme) |1 024 MB per App Service plan |1 024 MB per app |Saknas |Saknas |Saknas |Saknas |
| Bandbredd |165 MB |Obegränsade [dataöverföringspriser](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsade [dataöverföringspriser](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsade [dataöverföringspriser](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsade [dataöverföringspriser](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsade [dataöverföringspriser](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |
| Programmets arkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Webbsocketar per instans<sup>7</sup> |5 |35 |350 |Obegränsat |Obegränsat |Obegränsat |
| Utgående IP-anslutningar per instans | 600 | 600 | Beror på instansstorlek<sup>8</sup> | Beror på instansstorlek<sup>8</sup> | Beror på instansstorlek<sup>8</sup> | 16 000 |
| Samtidiga [felsökningsanslutningar](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per program |1 |1 |1 |5 |5 |5 |
| App Service certifikat per prenumeration<sup>9</sup>| Stöds inte | Stöds inte |10 |10 |10 |10 |
| Anpassade domäner per app</a> |0 (azurewebsites.net endast underdomän)|500 |500 |500 |500 |500 |
| [SSL-stöd för anpassad domän](../articles/app-service/configure-ssl-certificate.md) |Stöds inte, jokerteckencertifikat för \* .azurewebsites.net tillgängligt som standard|Stöds inte, jokerteckencertifikat för \* .azurewebsites.net tillgängligt som standard|Obegränsade SNI SSL anslutningar |Obegränsad SNI SSL och 1 IP SSL anslutningar ingår |Obegränsad SNI SSL och 1 IP SSL anslutningar ingår | Obegränsad SNI SSL och 1 IP SSL anslutningar ingår|
| Hybridanslutningar | | | 5 per plan | 25 per plan | 200 per app | 200 per app |
| [Integrering med virtuellt nätverk](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Privata slutpunkter](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 per app  |    |
| Integrerad lastbalanserare | |X |X |X |X |X<sup>10</sup> |
| [Åtkomstbegränsningar](../articles/app-service/networking-features.md#access-restrictions) | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app | 512 regler per app |
| [Alltid på](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Schemalagda säkerhetskopieringar](../articles/app-service/manage-backup.md) | | | | Schemalagda säkerhetskopieringar varannan timme, högst 12 säkerhetskopieringar per dag (manuell + schemalagd) | Schemalagda säkerhetskopieringar varje timme, högst 50 säkerhetskopieringar per dag (manuell + schemalagd) | Schemalagda säkerhetskopieringar varje timme, högst 50 säkerhetskopieringar per dag (manuell + schemalagd) |
| [Automatisk skalning](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Mellanlagringsplatser](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| [Testing in Production](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnostikloggar](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Autentisering och auktorisering](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service hanterade certifikat (offentlig förhandsversion)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|

<sup>1</sup> Appar och lagringskvoter per App Service plan annat anges.

<sup>2</sup> Det faktiska antalet appar som du kan vara värd för på dessa datorer beror på appaktiviteten, storleken på datorinstanserna och motsvarande resursanvändning.

<sup>3</sup> Dedikerade instanser kan ha olika storlekar. Mer information finns i [App Service priser.](https://azure.microsoft.com/pricing/details/app-service/)

<sup>4</sup> Fler tillåts vid begäran.

<sup>5</sup> Lagringsgränsen är den totala innehållsstorleken för alla appar i samma App Service-plan. Den totala innehållsstorleken för alla appar i alla App Service-planer i en enskild resursgrupp och region får inte överstiga 500 GB. Kvoten för filsystemet för App Service appar bestäms av den sammanställda mängden App Service planer som skapats i en region och resursgrupp.

<sup>6</sup> Dessa resurser är begränsade av fysiska resurser på de dedikerade instanserna (instansstorleken och antalet instanser).

<sup>7</sup> Om du skalar en app på Basic-nivån till två instanser har du 350 samtidiga anslutningar för var och en av de två instanserna. För standardnivån och högre finns det inga teoretiska gränser för webbsocketar, men andra faktorer kan begränsa antalet webbsocketar. Det högsta antalet samtidiga begäranden som tillåts (definieras av ) är `maxConcurrentRequestsPerCpu` till exempel: 7 500 per liten virtuell dator, 15 000 per medelstor virtuell dator (7 500 x 2 kärnor) och 75 000 per stor virtuell dator (18 750 x 4 kärnor).

<sup>8</sup> De maximala IP-anslutningarna är per instans och beror på instansstorleken: 1 920 per B1/S1/P1V3-instans, 3 968 per B2/S2/P2V3-instans, 8 064 per B3/S3/P3V3-instans.

<sup>9</sup> Den App Service Certificate kvotgränsen per prenumeration kan ökas via en supportbegäran till en maxgräns på 200.

<sup>10</sup> App Service Isolerad SKU:er kan belastningsutjämnas internt med Azure Load Balancer, så det finns ingen offentlig anslutning från Internet. Därför måste vissa funktioner i en ILB isolerad App Service användas från datorer som har direkt åtkomst till ILB-nätverksslutpunkten.

<sup>11</sup> Kör anpassade körbara filer och/eller skript på begäran, enligt ett schema eller kontinuerligt som en bakgrundsaktivitet i din App Service instans. Always On krävs för kontinuerlig webjobs-körning. Det finns ingen fördefinierad gräns för hur många webbjobb som kan köras i en App Service instans. Det finns praktiska begränsningar som beror på vad programkoden försöker göra.

<sup>12</sup> Domänerna Överdomäner stöds inte. Endast utfärdande av standardcertifikat (jokerteckencertifikat är inte tillgängliga). Begränsat till endast ett kostnadsfritt certifikat per anpassad domän.
