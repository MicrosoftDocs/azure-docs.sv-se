---
title: ta med fil
description: ta med fil
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009693"
---
> [!NOTE]
> En webbapp kan ta lång tid efter 20 minuters inaktivitet. och endast förfrågningar till den faktiska webb programmet kan återställa timern. Om du visar appens konfiguration i Azure Portal eller gör förfrågningar till webbplatsen för avancerade verktyg ( `https://<app_name>.scm.azurewebsites.net` ) återställs inte timern. Om du ställer in att webbappen ska köra kontinuerliga eller schemalagda WebJobs (timer-trigger) aktiverar du inställningen **Always on** på webbappens Azure- **konfiguration** för att säkerställa att webbjobben körs på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig på [pris nivåerna](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, standard och Premium.
