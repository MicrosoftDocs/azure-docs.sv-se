---
title: ta med fil
description: ta med fil
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/11/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 78890c0596642b629482a6d24d17d83fb8ebc9a0
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147575"
---
| Resurs | Gräns |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximalt antal skalnings enheter | 10 per region<sup>1</sup> |
| Cachestorlek | 5 GiB per enhet<sup>2</sup> |
| Samtidiga Server dels anslutningar<sup>3</sup> per http-utfärdare | 2 048 per enhet<sup>4</sup> |
| Maximal storlek för cachelagrat svar | 2 MiB |
| Högsta princip dokument storlek | 256 KiB<sup>5</sup> |
| Maximalt antal anpassade Gateway-domäner per tjänst instans<sup>6</sup> | 20 |
| Maximalt antal CA-certifikat per tjänst instans<sup>7</sup> | 10 |
| Maximalt antal tjänst instanser per prenumeration<sup>8</sup> | 20 |
| Maximalt antal prenumerationer per tjänst instans<sup>8</sup> | 500 |
| Maximalt antal klient certifikat per tjänst instans<sup>8</sup> | 50 |
| Maximalt antal API: er per tjänst instans<sup>8</sup> | 50 |
| Maximalt antal API-åtgärder per tjänst instans<sup>8</sup> | 1 000 |
| Högsta total varaktighet för begäran<sup>8</sup> | 30 sekunder |
| Maximal buffrad nytto Last storlek<sup>8</sup> | 2 MiB |
| Maximal URL-storlek för begäran<sup>9</sup> | 4096 byte |
| Maximal längd på URL-Sök segment<sup>10</sup> | 260 tecken |
| Maximalt antal gatewayer för egen värd<sup>11</sup> | 25 |

<sup>1</sup> Skalnings gränser beror på pris nivån. Mer information om pris nivåerna och deras skalnings gränser finns [API Management prissättning](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Storleken per enhets cacheminne beror på pris nivån. Se [API Management priser](https://azure.microsoft.com/pricing/details/api-management/)för att se pris nivåerna och deras skalnings gränser.<br/>
<sup>3</sup> Anslutningar i pooler och återanvänds, om de inte uttryckligen stängs av Server delen.<br/>
<sup>4</sup> Den här gränsen är per enhet för nivåerna Basic, standard och Premium. Developer-nivån är begränsad till 1 024. Den här gränsen gäller inte för förbruknings nivån.<br/>
<sup>5</sup> Den här gränsen gäller för nivåerna Basic, standard och Premium. I förbruknings nivån är princip dokument storleken begränsat till 16 KiB.<br/>
<sup>6</sup> Flera anpassade domäner stöds endast på Developer-och Premium-nivåerna.<br/>
<sup>7</sup> CA-certifikat stöds inte i förbruknings nivån.<br/>
<sup>8</sup> Den här gränsen gäller enbart för förbruknings nivån. Det finns inga begränsningar i dessa kategorier för andra nivåer.<br/>
<sup>9</sup> Gäller endast för förbruknings nivån. Innehåller en sträng med upp till 2048 byte lång frågesträng.<br/>
<sup>10</sup> om du vill höja gränsen kontaktar du [supporten](https://azure.microsoft.com/support/options/).<br/>
<sup>11</sup> Egen värdbaserade gatewayer stöds endast på utvecklings-och Premium-nivåerna. Gränsen gäller för antalet [egna värdbaserade gateway-resurser](/rest/api/apimanagement/2019-12-01/gateway). Kontakta [supporten](https://azure.microsoft.com/support/options/)om du vill höja gränsen. Observera att antalet noder (eller repliker) som är kopplade till en lokal gateway-resurs är obegränsat på Premium nivån och på en enskild nod på nivån utvecklare.