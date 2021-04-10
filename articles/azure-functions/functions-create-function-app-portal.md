---
title: Skapa din första funktion i Azure Portal
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 6ca187181d68d7924f97ebfac08e81dec77251e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492152"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Skapa din första funktion i Azure Portal

Med Azure Functions kan du köra din kod i en miljö utan server utan att först behöva skapa en virtuell dator (VM) eller publicera ett webb program. I den här artikeln får du lära dig hur du använder Azure Functions för att skapa en "Hello World" HTTP-utlösare i Azure Portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Vi rekommenderar istället att du [utvecklar dina funktioner lokalt](functions-develop-local.md) och publicerar dem i en Function-app i Azure.  
Använd någon av följande länkar för att komma igång med din valda lokala utvecklings miljö och ditt språk:

| Visuell Studio-kod | Terminal/kommando-prompt | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Kom igång med C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Kom igång med Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Kom igång med Java Script](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Kom igång med PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Kom igång med python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Kom igång med C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Kom igång med Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Kom igång med Java Script](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Kom igång med PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Kom igång med python](./create-first-function-cli-python.md) | [Kom igång med C #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Skapa sedan en funktion i appen ny funktion.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Skapa en HTTP-utlösnings funktion

1. På den vänstra menyn i fönstret **funktioner** väljer du **Functions** och väljer sedan **Lägg till** på den översta menyn. 
 
1. I fönstret **Lägg till funktion** väljer du mallen **http-utlösare** .

    ![Välj funktionen HTTP-utlösare](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Under **mall information** Använd `HttpExample` för **ny funktion** väljer du **Anonym** i list rutan **[Autentiseringsnivå](functions-bindings-http-webhook-trigger.md#authorization-keys)** och väljer sedan **Lägg till**.

    Azure skapar funktionen HTTP-utlösare. Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I din nya funktion för HTTP-utlösare väljer du **kod + test** på den vänstra menyn och sedan **Hämta funktions webb adress** på den översta menyn.

    ![Välj Hämta funktions webb adress](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. I dialog rutan **Hämta funktions webb adress** väljer du **standard** i list rutan och väljer sedan ikonen **Kopiera till Urklipp** . 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngen `?name=<your_name>` i slutet av den här URL: en och tryck på RETUR för att köra begäran. 

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Om URL: en för begäran innehöll en [åtkomst nyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) ( `?code=...` ), innebär det att du väljer **funktion** i stället för **Anonym** åtkomst nivå när du skapar funktionen. I så fall bör du istället lägga till `&name=<your_name>` .

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill se spårnings resultatet går du tillbaka till sidan **kod + test** i portalen och expanderar pilen **loggar** längst ned på sidan.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
