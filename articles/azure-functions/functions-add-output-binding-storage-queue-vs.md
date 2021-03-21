---
title: Ansluta funktioner till Azure Storage med hjälp av Visual Studio
description: Lär dig hur du lägger till en utgående bindning för att ansluta dina C#-klass biblioteks funktioner till en Azure Storage kö med Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 635392212027c73e5aa954eb671be31228796a0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185138"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Ansluta funktioner till Azure Storage med hjälp av Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du använder Visual Studio för att ansluta den funktion som du skapade i [föregående snabb starts artikel] till Azure Storage. Den utgående bindning som du lägger till i den här funktionen skriver data från HTTP-begäran till ett meddelande i en kö för Azure Queue Storage. 

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra det enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot är redan lagrad i en app-inställning med namnet `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln måste du: 

 - Slutför [snabb starten i del 1 av Visual Studio](./functions-create-your-first-function-visual-studio.md). 

- Logga in på din Azure-prenumeration från Visual Studio.

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

I [föregående snabb starts artikel](./create-first-function-vs-code-csharp.md)skapade du en Function-app i Azure tillsammans med det lagrings konto som krävs. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagrings kö i samma konto. Om du vill ansluta till ditt lagrings konto när du kör funktionen lokalt måste du hämta inställningarna för appen till *local.settings.jspå* filen. 

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. 

1. Under **åtgärder** väljer du **Redigera Azure App Service inställningar**. 

    ![Redigera program inställningarna](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Under **AzureWebJobsStorage**, kopierar du **värdet för fjärrsträng till** **lokalt** och väljer sedan **OK**. 

Lagrings bindningen, som använder `AzureWebJobsStorage` inställningen för anslutningen, kan nu ansluta till din Queue Storage när den körs lokalt.

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en kö för lagring av utdata måste du använda tillägget för lagrings bindningar installerade innan du kör projektet. Med undantag för HTTP-och timer-utlösare implementeras bindningar som tilläggs paket. 

1. Från **verktyg** -menyn väljer du **NuGet Package Manager**  >  **Package Manager-konsolen**. 

1. I-konsolen kör du följande [install-Package-](/nuget/tools/ps-ref-install-package) kommando för att installera lagrings tilläggen:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats kan du använda `name` bindningen för att komma åt den som ett attribut i Function-signaturen. Genom att använda en utgående bindning behöver du inte använda den Azure Storage SDK-koden för autentisering, hämta en Queue referens eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

En ny kö med namnet `outqueue` skapas i ditt lagrings konto av Functions-körningen när data bindningen först används. Du använder Cloud Explorer för att kontrol lera att kön har skapats tillsammans med det nya meddelandet.

## <a name="examine-the-output-queue"></a>Granska utdatakö

1. I Visual Studio från menyn **Visa** väljer du **Cloud Explorer**.

1. Expandera dina Azure-prenumerationer och **lagrings konton** i **Cloud Explorer** och expandera sedan det lagrings konto som används av din funktion. Om du inte kommer ihåg namnet på lagrings kontot kontrollerar du `AzureWebJobsStorage` inställningen för anslutnings strängen i *local.settings.js* filen.  

1. Expandera noden **köer** och dubbelklicka sedan på kön med namnet **disqueue** för att visa innehållet i kön i Visual Studio. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure* så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

    ![Köa meddelande visas i Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Kör funktionen igen, skicka en annan begäran och se att ett nytt meddelande visas i kön.  

Nu är det dags att publicera om den uppdaterade Function-appen till Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Distribuera om och verifiera den uppdaterade appen

1. I **Solution Explorer** högerklickar du på projektet och väljer **publicera**. Välj sedan **publicera** för att publicera projektet på Azure igen.

1. När distributionen är klar kan du använda webbläsaren igen för att testa den omdistribuerade funktionen. Som tidigare lägger du till frågesträngen `&name=<yourname>` i URL: en.

1. [Visa meddelandet i lagrings kön](#examine-the-output-queue) igen för att kontrol lera att utgående bindningen igen genererar ett nytt meddelande i kön.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar funktioner finns i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[föregående snabb starts artikel]: functions-create-your-first-function-visual-studio.md