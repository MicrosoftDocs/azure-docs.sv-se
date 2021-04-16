---
title: Konfigurera en funktion i Azure för att bearbeta data
titleSuffix: Azure Digital Twins
description: Se hur du skapar en funktion i Azure som kan komma åt och utlösas av digitala tvillingar.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480766"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Ansluta funktionsappar i Azure för bearbetning av data

Digitala tvillingar kan uppdateras baserat på data med hjälp av [händelsevägar via](concepts-route-events.md) beräkningsresurser. Till exempel kan en funktion som görs med hjälp av [Azure Functions](../azure-functions/functions-overview.md) uppdatera en digital tvilling som svar på:
* Enhettelemetridata från Azure IoT Hub.
* En egenskapsändring eller andra data från en annan digital tvilling i tvillingdiagrammet.

Den här artikeln visar hur du skapar en funktion i Azure för användning med Azure Digital Twins. Om du vill skapa en funktion följer du dessa grundläggande steg:

1. Skapa ett Azure Functions-projekt i Visual Studio.
2. Skriv en funktion som har en [Azure Event Grid](../event-grid/overview.md) utlösare.
3. Lägg till autentiseringskod i funktionen så att du kan komma åt Azure Digital Twins.
4. Publicera funktionsappen i Azure.
5. Konfigurera [säkerhet](concepts-security.md) för funktionsappen.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Krav: Konfigurera Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Skapa en funktionsapp i Visual Studio

I Visual Studio 2019 väljer du **Arkiv**  >  **nytt**  >  **projekt.** Sök efter **Azure Functions** mall. Välj **Nästa**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Skärmbild av Visual Studio som visar dialogrutan för det nya projektet. Den Azure Functions projektmallen är markerad.":::

Ange ett namn för funktionsappen och välj sedan __Skapa.__

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Skärmbild av Visual Studio visar dialogrutan för att konfigurera ett nytt projekt. Inställningarna omfattar projektnamn, spara plats, val för att skapa en ny lösning och lösningsnamn.":::

Välj den typ av **funktionsapp Event Grid utlösaren** och välj sedan __Skapa__.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Skärmbild av Visual Studio visar dialogrutan för att skapa ett nytt Azure Functions program. Alternativet Event Grid utlösare är markerat.":::

När funktionsappen har skapats Visual Studio ett kodexempel i en *Function1.cs-fil* i projektmappen. Den här korta funktionen används för att logga händelser.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Skärmbild av Visual Studio. Projektfönstret för det nya projektet visas. Kod för en exempelfunktion visas i en fil som heter Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Skriva en funktion som har en Event Grid utlösare

Du kan skriva en funktion genom att lägga till en SDK i funktionsappen. Funktionsappen interagerar med Azure Digital Twins med hjälp [av Azure Digital Twins SDK för .NET (C#).](/dotnet/api/overview/azure/digitaltwins/client) 

Om du vill använda SDK:n måste du inkludera följande paket i projektet. Installera paketen med hjälp Visual Studio NuGet-pakethanteraren. Eller lägg till paketen med `dotnet` hjälp av i ett kommandoradsverktyg.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

I nästa Visual Studio Solution Explorer du filen _Function1.cs_ som innehåller exempelkoden. Lägg till följande `using` -instruktioner för paketen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Lägga till autentiseringskod i funktionen

Deklarera nu variabler på klassnivå och lägg till autentiseringskod som gör att funktionen kan komma åt Azure Digital Twins. Lägg till variablerna och koden i funktionen i filen _Function1.cs._

* **Kod för att läsa url Azure Digital Twins tjänsten som en miljövariabel.** Det är en bra idé att läsa tjänst-URL:en från en miljövariabel i stället för att hårdkoda den i funktionen. Du anger värdet för den här miljövariabeln senare [i den här artikeln.](#set-up-security-access-for-the-function-app) Mer information om miljövariabler finns i [Hantera funktionsappen](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **En statisk variabel som ska innehålla en HttpClient-instans.** HttpClient är relativt dyrt att skapa, så vi vill undvika att skapa det för varje funktionsanrop.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Autentiseringsuppgifter för hanterad identitet.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **En lokal _variabel, DigitalTwinsClient_.** Lägg till variabeln i funktionen för att Azure Digital Twins klientinstansen. Gör *inte* den här variabeln statisk i klassen.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **En null-kontroll _för adtInstanceUrl_.** Lägg till null-kontrollen och omslut sedan funktionslogiken i ett try/catch-block för att fånga upp eventuella undantag.

Efter dessa ändringar ser funktionskoden ut som i följande exempel.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nu när programmet har skrivits kan du publicera det till Azure.

## <a name="publish-the-function-app-to-azure"></a>Publicera funktionsappen till Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Verifiera publiceringen av funktionen

1. Logga in med dina autentiseringsuppgifter i [Azure Portal](https://portal.azure.com/).
2. I sökrutan högst upp i fönstret söker du efter funktionsappens namn och väljer det sedan.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Skärmbild som visar Azure Portal. I sökfältet anger du funktionsappens namn." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. På sidan **Funktionsapp** som öppnas går du till menyn till vänster och väljer **Functions**. Om funktionen har publicerats visas dess namn i listan.

    > [!Note] 
    > Du kan behöva vänta några minuter eller uppdatera sidan några gånger innan funktionen visas i listan över publicerade funktioner.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Visa publicerade funktioner i Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

För att Azure Digital Twins åtkomst måste funktionsappen ha en system hanterad identitet med behörighet att komma åt din Azure Digital Twins instans. Du kommer att konfigurera det härnäst.

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurera säkerhetsåtkomst för funktionsappen

Du kan konfigurera säkerhetsåtkomst för funktionsappen med hjälp av antingen Azure CLI eller Azure Portal. Följ stegen för det alternativ du föredrar.

# <a name="cli"></a>[CLI](#tab/cli)

Kör dessa kommandon i [Azure Cloud Shell](https://shell.azure.com) eller en [lokal Azure CLI-installation.](/cli/azure/install-azure-cli)
Du kan använda funktionsappens system hanterade identitet för att ge **den rollen Azure Digital Twins dataägare** för din Azure Digital Twins instans. Rollen ger funktionsappen behörighet i instansen att utföra dataplansaktiviteter. Gör sedan instansens URL tillgänglig för din funktion genom att ange en miljövariabel.

### <a name="assign-an-access-role"></a>Tilldela en åtkomstroll

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Funktionsstommen i tidigare exempel kräver att en bearer-token skickas till den. Om bearer-token inte skickas kan funktionsappen inte autentisera med Azure Digital Twins. 

För att säkerställa att bearer-token skickas ställer du in behörigheter för [hanterade](../active-directory/managed-identities-azure-resources/overview.md) identiteter så att funktionsappen kan komma åt Azure Digital Twins. Du ställer bara in dessa behörigheter en gång för varje funktionsapp.


1. Använd följande kommando för att se information om den system hanterade identiteten för funktionen. Anteckna fältet `principalId` i utdata.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Om resultatet är tomt i stället för att visa identitetsinformation skapar du en ny system hanterad identitet för funktionen med hjälp av det här kommandot:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Utdata visar information om identiteten, inklusive det `principalId` värde som krävs för nästa steg. 

1. Använd värdet i följande kommando för att tilldela funktionsappens identitet `principalId` _till Azure Digital Twins dataägare_ för din Azure Digital Twins instans.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurera programinställningar

Gör din instanss URL tillgänglig för funktionen genom att ange en miljövariabel för den. Mer information om miljövariabler finns i [Hantera funktionsappen](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Den Azure Digital Twins instansens URL görs genom *att https://* i början av instansens värdnamn. Om du vill se värdnamnet, tillsammans med alla egenskaper för din instans, kör du `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Utför följande steg i [Azure Portal](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Tilldela en åtkomstroll

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

En system tilldelad hanterad identitet gör det möjligt för Azure-resurser att autentisera till molntjänster (till exempel Azure Key Vault) utan att lagra autentiseringsuppgifter i kod. När du aktiverar system tilldelad hanterad identitet kan alla nödvändiga behörigheter beviljas via rollbaserad åtkomstkontroll i Azure. 

Livscykeln för den här typen av hanterad identitet är kopplad till den här resursens livscykel. Dessutom kan varje resurs bara ha en system tilldelad hanterad identitet.

1. I [Azure Portal](https://portal.azure.com/)du efter funktionsappen genom att skriva dess namn i sökrutan. Välj din app i resultatet. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärmbild av Azure Portal. Funktionsappens namn finns i portalens sökfält och sökresultatet är markerat.":::

1. På funktionsappsidan går du till menyn till vänster och väljer Identitet __för__ att arbeta med en hanterad identitet för funktionen. På sidan __System tilldelad__ kontrollerar du att __Status__ har angetts till **På**. Om den inte är det anger du den nu och **sedan Spara** ändringen.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Skärmbild av Azure Portal. På sidan Identitet för funktionsappen är alternativet Status inställt på På." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Välj __Azure-rolltilldelningar.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Skärmbild av Azure Portal. På sidan Identitet för Azure-funktionen, under Behörigheter, är knappen Azure-rolltilldelningar markerad." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Välj __+ Lägg till rolltilldelning (förhandsversion)__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Skärmbild av Azure Portal. På sidan Azure-rolltilldelningar är knappen Lägg till rolltilldelning (förhandsversion) markerad." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. På sidan __Lägg till rolltilldelning (förhandsversion)__ väljer du följande värden:

    * **Omfång:** _Resursgrupp_
    * **Prenumeration**: Välj din Azure-prenumeration.
    * **Resursgrupp:** Välj din resursgrupp.
    * **Roll:** _Azure Digital Twins dataägare_

    Spara informationen genom att välja __Spara__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Skärmbild av Azure Portal visar hur du lägger till en ny rolltilldelning. Dialogrutan visar fält för Omfång, Prenumeration, Resursgrupp och Roll.":::

### <a name="configure-application-settings"></a>Konfigurera programinställningar

Om du vill göra URL Azure Digital Twins instansen tillgänglig för funktionen kan du ange en miljövariabel. Programinställningar exponeras som miljövariabler för att tillåta åtkomst Azure Digital Twins instansen. Mer information om miljövariabler finns i [Hantera funktionsappen](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Om du vill ange en miljövariabel med URL:en för din instans ska du först hitta instansens värdnamn: 

1. Sök efter din instans i [Azure Portal](https://portal.azure.com). 
1. I menyn till vänster väljer du __Översikt.__ 
1. Kopiera värdet __för Värdnamn.__

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Skärmbild av Azure Portal. Värdnamnsvärdet är markerat på instansens översiktssida.":::

Nu kan du skapa en programinställning:

1. I portalens sökfält söker du efter funktionsappen och väljer den sedan i resultaten.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärmbild av Azure Portal. Funktionsappens namn genomsöks i portalens sökfält. Sökresultatet är markerat.":::

1. Till vänster väljer du __Konfiguration__. På fliken __Programinställningar väljer__ du __+ Ny programinställning.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Skärmbild av Azure Portal. På fliken Konfiguration för funktionsappen är knappen för att skapa en ny programinställning markerad.":::

1. I fönstret som öppnas använder du värdet för värdnamnet som du kopierade för att skapa en programinställning.
    * **Namn:** ADT_SERVICE_URL
    * **Värde:** https://{your-azure-digital-twins-host-name}
    
    Välj __OK__ för att skapa en programinställning.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Skärmbild av Azure Portal. På sidan Lägg till/redigera programinställning fylls fälten Namn och Värde i. Knappen O K är markerad.":::

1. När du har skapat inställningen bör den visas på __fliken Programinställningar.__ Kontrollera **ADT_SERVICE_URL** visas i listan. Spara sedan den nya programinställningen genom att välja __Spara.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Skärmbild av Azure Portal. På fliken programinställningar är den nya inställningen A D T SERVICE U R L markerad. Knappen Spara är också markerad.":::

1. Alla ändringar av programinställningarna kräver en omstart av programmet, så välj __Fortsätt för__ att starta om programmet när du uppmanas att göra det.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Skärmbild av Azure Portal. En anteckning visar att eventuella ändringar i programinställningarna startar om programmet. Knappen Fortsätt är markerad.":::

---

## <a name="next-steps"></a>Nästa steg

I den här artikeln ställer du in en funktionsapp i Azure för användning med Azure Digital Twins. Se sedan hur du bygger vidare på din grundläggande funktion för [att mata in IoT Hub data i Azure Digital Twins](how-to-ingest-iot-hub-data.md).
