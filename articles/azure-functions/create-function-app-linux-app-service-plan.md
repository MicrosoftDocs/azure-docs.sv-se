---
title: Skapa en Function-app på Linux från Azure Portal
description: Lär dig hur du skapar din första Azure-funktion på Linux med hjälp av Azure Portal.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 34a93795b5e041ccef8e9576f97092e16c429444
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937186"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Skapa en Function-app i Linux i ett Azure App Service plan

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardcontainer. Den här artikeln beskriver hur du använder [Azure Portal](https://portal.azure.com) för att skapa en Linux-värdbaserad Function-app som körs i en [App Service plan](dedicated-plan.md). Du kan även [använda en egen anpassad container](functions-create-function-linux-custom-image.md).

![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Med den kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser. I den här artikeln skapar du en App Service plan när du skapar din Function-app.

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På den **nya** sidan väljer du **Compute**  >  **Funktionsapp**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Skapa en funktionsapp i Azure-portalen":::

1. På sidan **grundläggande** inställningar använder du funktionen appinställningar som anges i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsappens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.  |
    |**Publicera**| **Kod** (standard) | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.net Core** för C# och F # functions. |
    |**Version**| Versionsnummer | Välj den version av den installerade körnings miljön.  |
    |**Region**| Önskad region | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Sidan Grundinställningar":::

1. Välj **Nästa: Värd**. Ange följande inställningar på sidan **värd** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får bara innehålla siffror och gemena bokstäver. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operativsystem**| **Linux** | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **[Planera](../azure-functions/functions-scale.md)** | **Förbrukning (serverlös)** | Värdplan som definierar hur resurser allokeras till din funktionsapp. I standardplanen för **Förbrukning** läggs resurser till dynamiskt när de krävs av funktionerna. I den här värdbaserade [servern](https://azure.microsoft.com/overview/serverless-computing/) betalar du bara för den tid som dina funktioner körs. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Värd sida":::

1. Välj **Nästa: övervakning**. Ange följande inställningar på sidan **övervakning** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Ja** (standard) | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen eller välja **Skapa ny** kan du ändra Application Insights namn eller välja en annan region i ett [Azure-geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill lagra dina data. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Sidan övervakning":::

1. Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. På sidan **Granska + skapa** granskar du inställningarna och väljer sedan **skapa** för att etablera och distribuera Function-appen.

1. Välj **aviserings** ikonen i det övre högra hörnet i portalen och titta efter ett meddelande om att **distributionen har slutförts** .

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

    ![Distributionsmeddelande](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Även efter att din Function-app är tillgänglig kan det ta några minuter innan den är helt initierad.

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Skapa en HTTP-utlösnings funktion

Det här avsnittet visar hur du skapar en funktion i din nya Function-app i portalen.

> [!NOTE]
> Portal utvecklings upplevelsen kan vara användbar för att testa Azure Functions. I de flesta fall bör du överväga att utveckla dina funktioner lokalt och publicera projektet till din Function-app med hjälp av [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) eller [Azure Functions Core tools](functions-run-local.md#create-a-local-functions-project).  

1. På den vänstra menyn i fönstret **funktioner** väljer du **Functions** och väljer sedan **Lägg till** på den översta menyn. 
 
1. Välj **http-utlösare** i fönstret **ny funktion** .

    ![Välj funktionen HTTP-utlösare](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. I fönstret **ny funktion** godkänner du standard namnet för **ny funktion** eller anger ett nytt namn. 

1. Välj **Anonym** i list rutan **Autentiseringsnivå** och välj sedan **skapa funktion**.

    Azure skapar funktionen HTTP-utlösare. Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I din nya funktion för HTTP-utlösare väljer du **kod + test** på den vänstra menyn och sedan **Hämta funktions webb adress** på den översta menyn.

    ![Välj Hämta funktions webb adress](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. I dialog rutan **Hämta funktions webb adress** väljer du **standard** i list rutan och väljer sedan ikonen **Kopiera till Urklipp** . 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngen `?name=<your_name>` i slutet av den här URL: en och tryck på RETUR för att köra begäran. 

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill se spårnings resultatet går du tillbaka till sidan **kod + test** i portalen och expanderar pilen **loggar** längst ned på sidan.

   ![Funktionsloggvisning i Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en Function-app med en enkel HTTP-utlösnings funktion.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information finns i [Azure Functions HTTP bindings](functions-bindings-http-webhook.md) (HTTP-bindningar i Azure Functions).