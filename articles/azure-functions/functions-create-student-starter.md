---
title: Skapa en funktion med Start programmet för Azure för studenter
description: Lär dig hur du skapar en Azure-funktion från en Azure for student starter-prenumeration
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182537"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Skapa en funktion med Start programmet för Azure för studenter

I den här självstudien skapar vi en "Hello World"-HTTP-funktion i en start prenumeration för Azure för studenter. Vi går också igenom vad som är tillgängligt i Azure Functions i den här prenumerations typen.

*Start programmet för Microsoft Azure för studenter* hjälper dig att komma igång med de Azure-produkter du behöver för att utveckla i molnet utan kostnad. [Läs mer om det här erbjudandet här.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/solutions/serverless/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. [Lär dig mer om funktioner här.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Skapa en funktion

 I den här artikeln lär du dig hur du använder Azure Functions för att skapa en "Hello World" HTTP-utlösare i Azure Portal.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Skapa en HTTP-utlösnings funktion

1. På den vänstra menyn i fönstret **funktioner** väljer du **Functions** och väljer sedan **Lägg till** på den översta menyn. 
 
1. Välj **http-utlösare** i fönstret **ny funktion** .

    ![Välj funktionen HTTP-utlösare](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. I fönstret **ny funktion** godkänner du standard namnet för **ny funktion** eller anger ett nytt namn. 

1. Välj **Anonym** i list rutan **Autentiseringsnivå** och välj sedan **skapa funktion**.

    Azure skapar funktionen HTTP-utlösare. Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I din nya funktion för HTTP-utlösare väljer du **kod + test** på den vänstra menyn och sedan **Hämta funktions webb adress** på den översta menyn.

    ![Välj Hämta funktions webb adress](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. I dialog rutan **Hämta funktions webb adress** väljer du **standard** i list rutan och väljer sedan ikonen **Kopiera till Urklipp** . 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngen `?name=<your_name>` i slutet av den här URL: en och tryck på RETUR för att köra begäran. 

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill se spårnings resultatet går du tillbaka till sidan **kod + test** i portalen och expanderar pilen **loggar** längst ned på sidan.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Funktioner som stöds i Start programmet för Azure för studenter

I Start programmet för Azure för studenter har du till gång till de flesta funktioner i Azure Functions runtime, med flera viktiga begränsningar i listan nedan:

* HTTP-utlösaren är den enda utlösnings typ som stöds.
    * Alla indata och alla utgående bindningar stöds! [Se den fullständiga listan här.](functions-triggers-bindings.md)
* Språk som stöds: 
    * C# (.NET Core 2)
    * Java Script (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [Se språk som stöds i högre planer här](supported-languages.md)
* Windows är det enda operativ system som stöds.
* Skalningen är begränsad till [en instans på en kostnads fri nivå](https://azure.microsoft.com/pricing/details/app-service/windows/) som körs i upp till 60 minuter varje dag. Du ska serverlessly skala från 0 till 1 instans automatiskt när HTTP-trafik tas emot, men inte längre.
* Endast [version 2. x och senare](functions-versions.md) av Functions-körningen stöds.
* Alla verktyg för utvecklare stöds för redigerings-och publicerings funktioner. Detta inkluderar VS Code, Visual Studio, Azure CLI och Azure Portal. Om du vill använda något annat än portalen måste du först skapa en app i portalen och sedan välja appen som ett distributions mål i önskat verktyg.

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en Function-app med en enkel HTTP-utlösnings funktion. Härnäst kan du utforska lokala verktyg, fler språk, övervakning och integreringar.

 * [Skapa din första funktion med Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Skapa din första funktion med Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Azure Functions JavaScript-guide för utvecklare](./functions-reference-node.md)
 * [Använd Azure Functions för att ansluta till en Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Läs mer om hur du Azure Functions HTTP-bindningar](./functions-bindings-http-webhook.md).
 * [Övervaka dina Azure Functions](./functions-monitoring.md)