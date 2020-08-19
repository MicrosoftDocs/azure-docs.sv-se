---
title: inkludera fil
description: inkludera fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731050"
---
Azure Functions gör det enkelt att lägga till Application Insights integration till en Function-app från [Azure Portal].

1. I [Azure Portal][Azure Portal]söker du efter och väljer **Function-appen**och väljer sedan din Function-app. 

1. Välj **Application Insights inte har kon figurer ATS** överst i fönstret. Om du inte ser den här banderollen kanske din app redan har Application Insights aktive rad.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Aktivera Application Insights från portalen":::

1. Expandera **ändra din resurs** och skapa en Application Insights resurs genom att använda de inställningar som anges i följande tabell.  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nytt resursnamn** | Unikt namn på App | Det är enklast att använda samma namn som din Function-app, vilket måste vara unikt i din prenumeration. | 
    | **Plats** | Europa, västra | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som din Function-app eller en som är nära den regionen. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Skapa en Application Insights resurs":::

1. Välj **Använd**. 

   Application Insights resursen skapas i samma resurs grupp och prenumeration som din Function-app. När resursen har skapats stänger du Application Insightss fönstret.

1. I din Function-app väljer du **konfiguration** under **Inställningar**och väljer sedan **program inställningar**. Om du ser en inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY` är Application Insights integration aktive rad för din Function-app som körs i Azure.

[Azure Portal]: https://portal.azure.com
