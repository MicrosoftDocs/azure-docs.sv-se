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
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88009816"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I dialog rutan **publicera** väljer du **Azure** for **target** och väljer sedan **Nästa**. 

1. Välj **Azure WebJobs** för ett **särskilt mål** och välj sedan **Nästa**.

1. Välj **skapa ett nytt Azure-webbjobb**.

   ![Välj publicerings mål](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Använd värd inställningarna i följande tabell i dialog rutan **App Service (Windows)** .

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[Värdplan](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | En [App Service-plan](../articles/app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app. Du kan spara pengar när du är värd för flera appar genom att konfigurera webbapparna så att de delar en enda App Service-plan. App Service planer definierar region, instans storlek, skalnings antal och SKU (kostnads fri, delad, Basic, standard eller Premium). Välj **ny** för att skapa en ny app service plan. |

    ![Dialogrutan Skapa App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Välj **skapa** för att skapa ett webb jobb och relaterade resurser i Azure med de här inställningarna och distribuera projekt koden.