---
title: Konfigurera en funktion i Azure för bearbetning av data
titleSuffix: Azure Digital Twins
description: Se hur du skapar en funktion i Azure som kan komma åt och utlösas av digitala dubbla.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7bb9b6d4a6ca006952d709244e6526345d44431e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630274"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Anslut funktions appar i Azure för att bearbeta data

Uppdatering av digitala dubbla objekt baserat på data hanteras med hjälp av [**händelse vägar**](concepts-route-events.md) genom beräknings resurser, till exempel en funktion som görs med hjälp av [Azure Functions](../azure-functions/functions-overview.md). Funktioner kan användas för att uppdatera ett digitalt som svar på:
* data från enheten som kommer från IoT Hub
* egenskaps ändring eller andra data som kommer från ett annat digitalt värde i den dubbla grafen

Den här artikeln vägleder dig genom att skapa en funktion i Azure för användning med Azure Digital-dubbla. 

Här är en översikt över de steg som den innehåller:

1. Skapa ett Azure Functions-projekt i Visual Studio
2. Skriv en funktion med en [Event Grid](../event-grid/overview.md) -utlösare
3. Lägg till en autentiseringsnyckel till funktionen (för att kunna komma åt Azure Digital-dubbla)
4. Publicera funktionsappen till Azure
5. Konfigurera [säkerhets](concepts-security.md) åtkomst för Function-appen

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Förutsättning: Konfigurera Azure Digitals dubbla instanser

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Skapa en Function-app i Visual Studio

I Visual Studio 2019 väljer du _fil > nytt > projekt_ och söker efter _Azure Functions_ -mallen. Välj _Nästa_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Skärm bild av Visual Studio som visar dialog rutan nytt projekt. Den Azure Functions projekt mal len är markerad.":::

Ange ett namn för Function-appen och välj _skapa_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Skärm bild av Visual Studio som visar dialog rutan för att konfigurera ett nytt projekt, inklusive projekt namn, spara plats, val för att skapa en ny lösning och lösnings namn.":::

Välj programmets typ av *Event Grid utlösare* och välj _skapa_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Skärm bild av Visual Studio som visar dialog rutan för att skapa ett nytt Azure Functions-program. Alternativet Event Grid utlösare är markerat.":::

När din Function-app har skapats kommer Visual Studio att generera ett kod exempel i en **Function1.cs** -fil i projektmappen. Den här korta funktionen används för att logga händelser.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Skärm bild av Visual Studio i projekt fönstret för det nya projektet som har skapats. Det finns en kod för en exempel funktion som kallas Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Skriv en funktion med en Event Grid-utlösare

Du kan skriva en funktion genom att lägga till SDK i din Function-app. Function-appen interagerar med Azure Digitals dubbla med [Azure Digitals-SDK för .net (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

För att kunna använda SDK måste du inkludera följande paket i projektet. Du kan antingen installera paketen med hjälp av Visual Studios NuGet Package Manager eller lägga till paketen med `dotnet` i ett kommando rads verktyg.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .net. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Öppna sedan _Function1.cs_ -filen i Visual Studio-Solution Explorer där du har exempel kod och Lägg till följande `using` instruktioner för dessa paket i din funktion.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Lägg till autentiserings kod till funktionen

Du kommer nu att deklarera variabler på klass nivå och lägga till en kod som gör det möjligt att komma åt Azure Digitals dubbla. Du lägger till följande i din funktion i _Function1.cs_ -filen.

* Kod för att läsa URL: en för Azure Digitals dubblare-tjänst som en **miljö variabel**. Det är en bra idé att läsa tjänste-URL: en från en miljö variabel, i stället för att hårdkoda den i-funktionen. Du anger värdet för den här miljövariabeln [senare i den här artikeln](#set-up-security-access-for-the-function-app). Mer information om miljövariabler finns i [*Hantera din Function-app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* En statisk variabel som innehåller en HttpClient-instans. HttpClient är relativt dyrt att skapa och vi vill undvika att behöva göra detta för varje funktions anrop.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Du kan använda autentiseringsuppgifterna för hanterad identitet i Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Lägg till en lokal variabel _DigitalTwinsClient_ inuti din funktion för att hålla din Azure Digital-klient instans. Gör *inte* den här variabeln statisk i klassen.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Lägg till en null-kontroll för _adtInstanceUrl_ och packa upp funktions logiken i ett try/catch-block för att fånga eventuella undantag.

Efter dessa ändringar ser funktions koden ut ungefär så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nu när ditt program är skrivet kan du publicera det till Azure med hjälp av stegen i nästa avsnitt.

## <a name="publish-the-function-app-to-azure"></a>Publicera funktionsappen till Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>Verifiera funktionen publicera

1. Logga in med dina autentiseringsuppgifter i [Azure Portal](https://portal.azure.com/).
2. I Sök fältet högst upp i fönstret söker du efter namnet på din **funktion i appen**.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Sök efter din Function-app med namnet i Azure Portal." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. På sidan *Function-appen* som öppnas väljer du *funktioner* i meny alternativen till vänster. Om din funktion har publicerats visas funktions namnet i listan.
Observera att du kan behöva vänta några minuter eller uppdatera sidan ett par gånger innan du kan se din funktion listad i listan publicerade funktioner.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Visa publicerade funktioner i Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

För att din Function-app ska kunna komma åt Azure Digitals, måste den ha en Systemhanterad identitet med behörigheter för åtkomst till din Azure Digital-instansen. Nu ska du ställa in det här.

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurera säkerhets åtkomst för Function-appen

Du kan ställa in säkerhets åtkomst för Function-appen med hjälp av antingen Azure CLI eller Azure Portal. Följ anvisningarna för önskat alternativ nedan.

# <a name="cli"></a>[CLI](#tab/cli)

Du kan köra dessa kommandon i [Azure Cloud Shell](https://shell.azure.com) eller en [lokal Azure CLI-installation](/cli/azure/install-azure-cli).

### <a name="assign-access-role"></a>Tilldela åtkomst roll

Funktionen Skeleton från tidigare exempel kräver att en Bearer-token skickas till den, för att kunna autentisera med Azure Digital-dubbla. För att se till att denna Bearer-token överförs måste du konfigurera [hanterad tjänstidentitet (MSI)](../active-directory/managed-identities-azure-resources/overview.md) -behörigheter för Function-appen för att få åtkomst till Azure Digital-dubbla. Detta måste bara göras en gång för varje Function-app.

Du kan använda Function-appens systemhanterade identitet för att ge den _**Azure Digitals data ägar**_ roll för Azure Digitals-instansen. Detta ger funktionen app behörighet i instansen för att utföra data Plans aktiviteter. Sedan kan du göra webb adressen till Azure Digitals dubblare-instans tillgänglig för din funktion genom att ange en miljö variabel.

1. Använd följande kommando för att se information om den systemhanterade identiteten för funktionen. Anteckna fältet _principalId_ i utdata.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Om resultatet är tomt i stället för att visa information om en identitet skapar du en ny Systemhanterad identitet för funktionen med hjälp av det här kommandot:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Utdata visar sedan information om identiteten, inklusive det _principalId_ -värde som krävs för nästa steg. 

1. Använd _principalId_-värdet i följande kommando för att tilldela funktionsappens identitet till _Azure Digital Twins-dataägarrollen_ för Azure Digital-instansen.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurera programinställningar

Slutligen kan du göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion genom att ange en **miljö variabel** för den. Mer information om miljövariabler finns i [*Hantera din Function-app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet* för din Azure Digital-instansen. Om du vill se värd namnet, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Utför följande steg i [Azure Portal](https://portal.azure.com/).

### <a name="assign-access-role"></a>Tilldela åtkomst roll

En systemtilldelad hanterad identitet gör det möjligt för Azure-resurser att autentisera till moln tjänster (till exempel Azure Key Vault) utan att lagra autentiseringsuppgifter i kod. När den är aktive rad kan alla nödvändiga behörigheter beviljas via rollbaserad åtkomst kontroll i Azure. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för den här resursen. Dessutom kan varje resurs bara ha en systemtilldelad hanterad identitet.

1. I [Azure Portal](https://portal.azure.com/)söker du efter din Function-app genom att skriva dess namn i Sök fältet. Välj din app från resultaten. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärm bild av Azure Portal: namnet på appens funktion genomsöks i portalens sökfält och Sök resultatet är markerat.":::

1. På sidan Function-appen väljer du _identitet_ i navigerings fältet till vänster för att arbeta med en hanterad identitet för funktionen. På sidan _systemtilldelat_ , kontrollerar du att _statusen_ är inställd **på på** (om den inte är det, anger du den nu och *sparar* ändringen).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Skärm bild av Azure Portal: på sidan identitet för Function-appen är alternativet status inställt på on." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Välj knappen _Azure Role-tilldelningar_ som öppnar sidan *roll tilldelningar för Azure* .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Skärm bild av Azure Portal: en högdager runt knappen roll tilldelningar i Azure under behörigheter på Azure-funktionens identitets sida." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Välj _+ Lägg till roll tilldelning (för hands version)_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Skärm bild av Azure Portal: en högdager runt + Lägg till roll tilldelning (för hands version) på sidan roll tilldelningar för Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. På sidan _Lägg till roll tilldelning (förhands granskning)_ som öppnas, väljer du följande värden:

    * **Omfång**: Resursgrupp
    * **Prenumeration**: Välj din Azure-prenumeration
    * **Resurs grupp**: Välj din resurs grupp i list rutan
    * **Roll**: Välj _Azure Digitals sammanflätade data ägare_ i list rutan

    Spara sedan informationen genom att trycka på knappen _Spara_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Skärm bild av dialog rutan Azure Portal: för att lägga till en ny roll tilldelning (för hands version). Det finns fält för omfånget, prenumerationen, resurs gruppen och rollen.":::

### <a name="configure-application-settings"></a>Konfigurera programinställningar

Om du vill göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion kan du ange en **miljö variabel** för den. Mer information om miljövariabler finns i [*Hantera din Function-app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Program inställningarna visas som miljövariabler för att komma åt Azure Digitals-instansen. 

Om du vill ange en miljö variabel med URL: en för din instans, hämtar du först URL: en genom att hitta värd namnet för Azure Digital-instansen. Sök efter din instans i [Azure Portal](https://portal.azure.com) Sök fältet. Välj sedan _Översikt_ i det vänstra navigerings fältet för att visa _värd namnet_. Kopiera det här värdet.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Skärm bild av Azure Portal: på sidan Översikt för Azure Digital-instansen är värd namn svärdet markerat.":::

Nu kan du skapa en program inställning med följande steg:

1. Sök efter din Function-app i Portal Sök fältet och välj den från resultaten.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärm bild av Azure Portal: namnet på appens funktion genomsöks i portalens sökfält och Sök resultatet är markerat.":::

1. Välj _konfiguration_ i navigerings fältet till vänster. På fliken _program inställningar_ väljer du _+ ny program inställning_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Skärm bild av Azure Portal: på sidan konfiguration för Function-appen markeras knappen för att skapa en ny program inställning.":::

1. I fönstret som öppnas, använder du värd namn svärdet som du kopierade ovan för att skapa en program inställning.
    * **Namn**: ADT_SERVICE_URL
    * **Värde**: https://{Your-Azure-Digital-Garns-Host-Name}
    
    Välj _OK_ för att skapa en program inställning.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Skärm bild av Azure Portal: knappen OK är markerad när du har fyllt i fälten namn och värde på inställnings sidan Lägg till/redigera program.":::

1. När du har skapat inställningen bör du se att den visas igen på fliken _program inställningar_ . Verifiera *ADT_SERVICE_URL* visas i listan och spara sedan den nya program inställningen genom att välja knappen _Spara_ .

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Skärm bild av Azure Portal: Sidan program inställningar med den nya inställningen ADT_SERVICE_URL markerad. Knappen Spara markeras också.":::

1. Alla ändringar av program inställningarna kräver att en program omstart börjar gälla, så välj _Fortsätt_ för att starta om programmet när du uppmanas till det.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Skärm bild av Azure Portal: det finns ett meddelande om att program inställningarna har ändrats med starta om programmet. Knappen Fortsätt är markerad.":::

---

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du följt stegen för att ställa in en Function-app i Azure för användning med Azure Digital-dubbla.

Sedan kan du se hur du bygger på din grundläggande funktion för att mata in IoT Hub data i Azure Digitals:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)
