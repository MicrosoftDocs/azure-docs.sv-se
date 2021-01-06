---
title: Azure Functions förbruknings plan som värd
description: Lär dig mer om hur du använder Azure Function förbruknings plan för att köra din kod i en miljö som skalas dynamiskt, men som du bara betalar för resurser som används under körningen.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: c0619def4687935cd9e403563966b35b84f13c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937791"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions förbruknings plan som värd

När du använder förbruknings planen läggs instanser av Azure Functions-värden dynamiskt till och tas bort baserat på antalet inkommande händelser. Förbruknings planen är det helt <em>serverbaserade</em> värd alternativet för Azure Functions.

## <a name="benefits"></a>Fördelar

Förbruknings planen skalas automatiskt, till och med under perioder med hög belastning. När du kör funktioner i en förbruknings plan debiteras du bara för beräknings resurser när funktionerna körs. I en förbrukningsplan får en funktions körningstid timeout efter en konfigurerbar tidsperiod.

En jämförelse av förbruknings planen för den andra planen och värd typerna finns i [funktions skala och värd alternativ](functions-scale.md).

## <a name="billing"></a>Fakturering

Fakturering baseras på antalet körningar, körningstid och använt minne. Användningen sammanställs för alla funktioner i en Function-app. Mer information finns på sidan med [Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/).

Mer information om hur du beräknar kostnader när du kör i en förbruknings plan finns i [förstå förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Skapa en app för förbruknings Plans funktion

När du skapar en Function-app i Azure Portal är förbruknings planen standard. När du använder API: er för att skapa en Function-app behöver du inte först skapa en App Service plan som du gör med Premium och dedikerade planer.

Använd följande länkar om du vill lära dig hur du skapar en server lös Function-app i en förbruknings plan, antingen via programmering eller i Azure Portal:

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure-portalen](functions-create-first-azure-function.md)
+ [Azure Resource Manager-mall](functions-create-first-function-resource-manager.md)

Du kan också skapa Function-appar i en förbruknings plan när du publicerar ett Functions-projekt från [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) eller [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Flera appar i samma plan

Function-appar i samma region kan tilldelas samma förbruknings plan. Det finns ingen nack delar eller påverkan på att flera appar körs i samma förbruknings plan. Tilldelning av flera appar till samma förbruknings plan påverkar inte återhämtning, skalbarhet eller tillförlitlighet för varje app.

## <a name="next-steps"></a>Nästa steg

+ [Azure Functions värd alternativ](functions-scale.md)
+ [Händelse driven skalning i Azure Functions](event-driven-scaling.md)
