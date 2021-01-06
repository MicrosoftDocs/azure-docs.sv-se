---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 754ca10e72ca2274607e954748bfa8cb5286c6ab
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954538"
---
1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På den **nya** sidan väljer du **Compute**  >  **Funktionsapp**.

1. På sidan **grundläggande** inställningar använder du funktionen appinställningar som anges i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsappens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.  |
    |**Publicera**| Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.net Core** för C# och F # functions. |
    |**Version**| Versionsnummer | Välj den version av den installerade körnings miljön.  |
    |**Region**| Önskad region | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |

    ![Grunder](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Välj **Nästa: Värd**. Ange följande inställningar på sidan **värd** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../articles/storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får bara innehålla siffror och gemena bokstäver. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **[Planera](../articles/azure-functions/functions-scale.md)** | **Förbrukning (serverlös)** | Värdplan som definierar hur resurser allokeras till din funktionsapp. I standardplanen för **Förbrukning** läggs resurser till dynamiskt när de krävs av funktionerna. I den här värdbaserade [servern](https://azure.microsoft.com/overview/serverless-computing/) betalar du bara för den tid som dina funktioner körs. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Välj **Nästa: övervakning**. Ange följande inställningar på sidan **övervakning** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen eller välja **Skapa ny** kan du ändra Application Insights namn eller välja en annan region i ett [Azure-geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill lagra dina data. |

    ![Övervakning](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. På sidan **Granska + skapa** granskar du inställningarna och väljer sedan **skapa** för att etablera och distribuera Function-appen.

1. Välj **aviserings** ikonen i det övre högra hörnet i portalen och titta efter ett meddelande om att **distributionen har slutförts** .

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

    ![Distributionsmeddelande](./media/functions-create-function-app-portal/function-app-create-notification2.png)