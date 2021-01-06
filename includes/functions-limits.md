---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: aa75d1d57f44bb1a4d6513823ac97ac9917b260f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934477"
---
| Resurs |[Förbrukningsplan](../articles/azure-functions/consumption-plan.md)|[Premiumplan](../articles/azure-functions/functions-premium-plan.md)|[Dedikerad plan](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Standard [varaktighet för timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Längsta [timeout-varaktighet](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | obegränsat<sup>7</sup> | obegränsat<sup>2</sup> | unbounded | unbounded |
| Max. utgående anslutningar (per instans) | 600 aktiv (1200 totalt) | unbounded | unbounded | unbounded | unbounded |
| Maximal begär ande storlek (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Är beroende av kluster |
| Max. frågesträngs längd<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Är beroende av kluster |
| Max. URL-längd för begäran<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Är beroende av kluster |
|[ACU](../articles/virtual-machines/acu.md) per instans | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS-priser](https://azure.microsoft.com/pricing/details/container-service/) |
| Maximalt minne (GB per instans) | 1.5 | 3.5-14 | 1,75-14 | 3,5 – 14 | Alla noder stöds |
| Function-appar per plan |100 |100 |obegränsat<sup>4</sup> | unbounded | unbounded |
| [App Service-planer](../articles/app-service/overview-hosting-plans.md) | 100 per [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resurs grupp |100 per resurs grupp | - | - |
| Lagring<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | saknas |
| Anpassade domäner per app</a> |500<sup>6</sup> |500 |500 | 500 | saknas |
| Stöd för anpassad domän- [SSL](../articles/app-service/configure-ssl-bindings.md) |en obunden SNI SSL anslutning ingår | obegränsade SNI SSL och 1 IP SSL anslutningar ingår |obegränsade SNI SSL och 1 IP SSL anslutningar ingår | obegränsade SNI SSL och 1 IP SSL anslutningar ingår | saknas |

<sup>1</sup> som standard är tids gränsen för funktioner 1. x-körningsmiljön i en app service plan obegränsad.  
<sup>2</sup> kräver att App Service plan anges till [Always on](../articles/azure-functions/dedicated-plan.md#always-on). Betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> de här gränserna [anges på värden](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> det faktiska antalet funktions program som du kan vara värd för beror på aktivitetens appar, storleken på dator instanserna och motsvarande resursutnyttjande.  
<sup>5</sup> lagrings gränsen är den totala innehålls storleken i tillfällig lagring över alla appar i samma app service plan. Förbruknings planen använder Azure Files för tillfällig lagring.  
<sup>6</sup> när din Function-app finns i en [förbruknings plan](../articles/azure-functions/consumption-plan.md)stöds endast alternativet CNAME. För Function-appar i en [Premium-plan](../articles/azure-functions/functions-premium-plan.md) eller en [App Service plan](../articles/azure-functions/dedicated-plan.md)kan du mappa en anpassad domän med antingen en CNAME-eller en a-post.  
<sup>7</sup> garanterat i upp till 60 minuter.  
<sup>8</sup> arbetare är roller som är värdar för kund program. Arbetare är tillgängliga i tre fasta storlekar: en vCPU/3,5 GB RAM; Två vCPU/7 GB RAM-minne; Fyra vCPU/14 GB RAM-minne.