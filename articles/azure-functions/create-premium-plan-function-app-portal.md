---
title: Skapa en Azure Functions Premium-plan i portalen
description: Lär dig hur du använder Azure Portal för att skapa en Function-app som körs i Premium-planen.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 20921423247dda3cbb39b58dcc805dac6d367390
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937779"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Skapa en Premium Plans funktion-app i Azure Portal

Azure Functions erbjuder en skalbar Premium-plan som ger virtuell nätverks anslutning, ingen kall start och förstklassig maskin vara. Läs mer i [Azure Functions Premium-plan](functions-premium-plan.md). 

I den här artikeln får du lära dig hur du använder Azure Portal för att skapa en Function-app i en Premium-plan. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Nu kan du skapa funktioner i den nya Function-appen. Dessa funktioner kan dra nytta av fördelarna med [Premium planen](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till en HTTP-utlöst funktion](functions-create-first-azure-function.md#create-function)
