---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878694"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.   Azure våren Cloud erbjuder två pris nivåer: Basic och standard. Vi kommer att ha detalj begränsningar för båda nivåerna i den här artikeln.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure våren Cloud Service-nivåer och-gränser

| Resurs | Omfång | Basic | Standard
------- | ------- | -------
Virtuell processor | per app-instans | 1 | 4
Minne | per app-instans | 2 GB | 8 GB
Azure våren Cloud Service-instanser | per region per prenumeration | 10 | 10
Totalt antal App-instanser | per Azure våren Cloud Service-instans | 25 | 500
Anpassade domäner | per Azure våren Cloud Service-instans | 0 | 25 
Beständiga volymer | per Azure våren Cloud Service-instans | 1 GB/app x 10-appar | 50 GB/app x 10-appar

> [!TIP]
> Gränser som anges för totalt antal App-instanser per tjänst instans gäller för appar och distributioner i valfritt tillstånd, inklusive stoppat tillstånd. Ta bort appar eller distributioner som inte används.

## <a name="next-steps"></a>Nästa steg

Vissa standard gränser kan ökas. Om installationen kräver en ökning skapar du [en support förfrågan](../azure-portal/supportability/how-to-create-azure-support-request.md).
