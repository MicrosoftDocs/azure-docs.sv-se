---
title: Skapa din första funktion i Azure Portal
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 336e531f4ec64141770fc26d7e6eea9ebfedf922
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517151"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Skapa din första funktion i Azure Portal

Azure Functions kan du köra din kod i en serverlös miljö utan att först behöva skapa en virtuell dator (VM) eller publicera en webbapp. I den här artikeln får du lära dig hur du använder Azure Functions för att skapa en HTTP-utlösarfunktion "hello world" i Azure Portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Vi rekommenderar i stället att du [utvecklar dina funktioner lokalt](functions-develop-local.md) och publicerar till en funktionsapp i Azure.  
Använd någon av följande länkar för att komma igång med din valda lokala utvecklingsmiljö och språk:

| Visuell Studio-kod | Terminal/kommandotolk | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Kom igång med C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Kom igång med Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Kom igång med JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Kom igång med PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Kom igång med Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Kom igång med C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Kom igång med Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Kom igång med JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Kom igång med PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Kom igång med Python](./create-first-function-cli-python.md) | [Kom igång med C #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Skapa sedan en funktion i den nya funktionsappen.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Skapa en HTTP-utlösarfunktion

1. På den vänstra menyn i **funktionsfönstret** väljer du **Funktioner** och sedan Lägg **till** på den översta menyn. 
 
1. I fönstret **Lägg till funktion** väljer du mallen **HTTP-utlösare.**

    ![Välj HTTP-utlösarfunktion](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Under **Använd mallinformation** `HttpExample` för Ny **funktion** väljer du **Anonym** i **[listrutan](functions-bindings-http-webhook-trigger.md#authorization-keys)** Auktoriseringsnivå och väljer sedan Lägg **till.**

    Azure skapar HTTP-utlösarfunktionen. Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya HTTP-utlösarfunktionen **väljer du Kod + test** på den vänstra menyn och väljer sedan Hämta **funktions-URL** på den översta menyn.

    ![Välj Hämta funktions-URL](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. I dialogrutan **Hämta funktionswebbadress** väljer du **standard** i listrutan och väljer sedan ikonen Kopiera till **Urklipp.** 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngsvärdet `?name=<your_name>` i slutet av den här URL:en och tryck på Retur för att köra begäran. Webbläsaren bör visa ett svarsmeddelande som returnerar frågesträngens värde. 

    Om begärande-URL:en [inkluderade en åtkomstnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) ( ), innebär det att du väljer `?code=...` **Funktion** i stället **för Anonym** åtkomstnivå när du skapar funktionen. I det här fallet bör du i stället lägga till `&name=<your_name>` .

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill se spårningsutdata går du tillbaka till sidan Kod **+** test i portalen och expanderar pilen **Loggar** längst ned på sidan. Anropa funktionen igen för att se spårningsutdata som skrivits till loggarna. 

    :::image type="content" source="media/functions-create-first-azure-function/function-view-logs.png" alt-text="Functions-loggvisare i Azure Portal":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
