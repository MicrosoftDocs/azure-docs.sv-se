---
title: Konfigurera en funktion i Azure för bearbetning av data
titleSuffix: Azure Digital Twins
description: Se hur du skapar en funktion i Azure som kan komma åt och utlösas av digitala dubbla.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2419761c195258c60561e284abf0227b915ed4f6
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123640"
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

Du kan skriva en funktion genom att lägga till SDK i din Function-app. Function-appen interagerar med Azure Digitals dubbla med [Azure Digitals-SDK för .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

För att kunna använda SDK måste du inkludera följande paket i projektet. Du kan antingen installera paketen med hjälp av Visual Studios NuGet Package Manager eller lägga till paketen med `dotnet` i ett kommando rads verktyg. Följ stegen nedan för din önskade metod.

**Alternativ 1. Lägg till paket med hjälp av Visual Studio Package Manager:**
    
Högerklicka på projektet och välj _Hantera NuGet-paket_ i listan. Sedan väljer du fliken _Bläddra_ i fönstret som öppnas och söker efter följande paket. Välj _Installera_ och _Godkänn_ licens avtalet för att installera paketen.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core.Pipeline`

**Alternativ 2. Lägg till paket med `dotnet` kommando rads verktyget:**

Du kan också använda följande `dotnet add` kommandon i ett kommando rads verktyg:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

Öppna sedan _Function1.cs_ -filen i Visual Studio-Solution Explorer där du har exempel kod och Lägg till följande `using` instruktioner i din funktion. 

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

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurera säkerhets åtkomst för Function-appen

Du kan ställa in säkerhets åtkomst för Function-appen med hjälp av antingen Azure CLI eller Azure Portal. Följ anvisningarna för önskat alternativ nedan.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Alternativ 1: Konfigurera säkerhets åtkomst för Function-appen med CLI

Funktionen Skeleton från tidigare exempel kräver att en Bearer-token skickas till den, för att kunna autentisera med Azure Digital-dubbla. För att säkerställa att denna Bearer-token skickas måste du konfigurera [hanterad tjänstidentitet (MSI)](../active-directory/managed-identities-azure-resources/overview.md) för Function-appen. Detta måste bara göras en gång för varje Function-app.

Du kan skapa Systemhanterad identitet och tilldela Function-appen identitet till den _**digitala Azure-dataägarens data ägar**_ roll för Azure Digitals-instansen. Detta ger funktionen app behörighet i instansen för att utföra data Plans aktiviteter. Sedan kan du göra webb adressen till Azure Digitals dubblare-instans tillgänglig för din funktion genom att ange en miljö variabel.

Använd [Azure Cloud Shell](https://shell.azure.com) för att köra kommandona.

Använd följande kommando för att skapa den systemhanterade identiteten. Anteckna fältet _principalId_ i utdata.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Använd _principalId_-värdet i följande kommando för att tilldela funktionsappens identitet till _Azure Digital Twins-dataägarrollen_ för Azure Digital-instansen.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Slutligen kan du göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion genom att ange en **miljö variabel** för den. Mer information om miljövariabler finns i [*Hantera din Function-app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet* för Azure Digital-instansen. Om du vill se värd namnet, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Alternativ 2: Konfigurera säkerhets åtkomst för Function-appen med hjälp av Azure Portal

En systemtilldelad hanterad identitet gör det möjligt för Azure-resurser att autentisera till moln tjänster (till exempel Azure Key Vault) utan att lagra autentiseringsuppgifter i kod. När den är aktive rad kan alla nödvändiga behörigheter beviljas via rollbaserad åtkomst kontroll i Azure. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för den här resursen. Dessutom kan varje resurs (till exempel virtuell dator) bara ha en systemtilldelad hanterad identitet.

I [Azure Portal](https://portal.azure.com/)söker du efter _Function-appen_ i Sök fältet med namnet på den funktion som du skapade tidigare. Välj *Funktionsapp* i listan. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärm bild av Azure Portal: namnet på appens funktion genomsöks i portalens sökfält och Sök resultatet är markerat.":::

I fönstret Function-app väljer du _identitet_ i navigerings fältet till vänster för att aktivera hanterad identitet.
Under fliken _tilldelade system_ växlar du _statusen_ till på och _sparar_ den. Du kommer att se ett popup-fönster för att _Aktivera systemtilldelad hanterad identitet_.
Välj _Ja_ -knapp. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Skärm bild av Azure Portal: på sidan identitet för Function-appen är alternativet för att aktivera systemtilldelad hanterad identitet inställd på Ja. Alternativet status är inställt på on.":::

Du kan kontrol lera i meddelandena att din funktion har registrerats med Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Skärm bild av Azure Portal: listan meddelanden från val av Bell-formad ikon i portalens översta fält. Det finns ett meddelande om att användaren har aktiverat hanterad identitet som tilldelats av systemet.":::

Observera också det **objekt-ID** som visas på sidan _identitet_ , eftersom det kommer att användas i nästa avsnitt.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Skärm bild av Azure Portal: en högdager runt fältet objekt-ID från Azure-funktionens identitets sida.":::

### <a name="assign-access-roles-using-azure-portal"></a>Tilldela åtkomst roller med Azure Portal

Välj knappen _Azure Role-tilldelningar_ som öppnar sidan *roll tilldelningar för Azure* . Välj sedan _+ Lägg till roll tilldelning (för hands version)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Skärm bild av Azure Portal: en högdager runt knappen roll tilldelningar i Azure under behörigheter på Azure-funktionens identitets sida.":::

På sidan _Lägg till roll tilldelning (förhands granskning)_ som öppnas väljer du:

* _Omfång_: Resursgrupp
* _Prenumeration_: Välj din Azure-prenumeration
* _Resurs grupp_: Välj din resurs grupp i list rutan
* _Roll_: Välj _Azure Digitals sammanflätade data ägare_ i list rutan

Spara sedan informationen genom att trycka på knappen _Spara_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Skärm bild av dialog rutan Azure Portal: för att lägga till en ny roll tilldelning (för hands version). Det finns fält för omfånget, prenumerationen, resurs gruppen och rollen.":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurera program inställningar med Azure Portal

Om du vill göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion kan du ange en **miljö variabel** för den. Mer information om miljövariabler finns i [*Hantera din Function-app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Program inställningarna visas som miljövariabler för att komma åt Azure Digitals-instansen. 

Om du vill ange en miljö variabel med URL: en för din instans, hämtar du först URL: en genom att hitta värd namnet för Azure Digital-instansen. Sök efter din instans i [Azure Portal](https://portal.azure.com) Sök fältet. Välj sedan _Översikt_ i det vänstra navigerings fältet för att visa _värd namnet_. Kopiera det här värdet.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Skärm bild av Azure Portal: på sidan Översikt för Azure Digital-instansen är värd namn svärdet markerat.":::

Nu kan du skapa en program inställning genom att följa stegen nedan:

1. Sök efter din Function-app i Portal Sök fältet och välj den från resultaten
1. Välj _konfiguration_ i navigerings fältet till vänster för att skapa en ny program inställning
1. På fliken _program inställningar_ väljer du _+ ny program inställning_

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Skärm bild av Azure Portal: namnet på appens funktion genomsöks i portalens sökfält och Sök resultatet är markerat.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Skärm bild av Azure Portal: på sidan konfiguration för Function-appen markeras knappen för att skapa en ny program inställning.":::

I fönstret som öppnas, använder du värd namn svärdet som du kopierade ovan för att skapa en program inställning.
* **Namn**: ADT_SERVICE_URL
* **Värde**: https://{Your-Azure-Digital-Garns-Host-Name}

Välj _OK_ för att skapa en program inställning.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Skärm bild av Azure Portal: knappen OK är markerad när du har fyllt i fälten namn och värde på inställnings sidan Lägg till/redigera program.":::

Du kan visa dina program inställningar med program namn under fältet _namn_ . Spara sedan program inställningarna genom att välja knappen _Spara_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Skärm bild av Azure Portal: Sidan program inställningar med den nya inställningen ADT_SERVICE_URL markerad. Knappen Spara markeras också.":::

Eventuella ändringar av program inställningarna kräver att en program omstart börjar gälla. Välj _Fortsätt_ för att starta om programmet.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Skärm bild av Azure Portal: det finns ett meddelande om att program inställningarna har ändrats med starta om programmet. Knappen Fortsätt är markerad.":::

Du kan visa att program inställningarna uppdateras genom att välja _meddelande_ ikon. Om program inställningen inte skapas kan du försöka lägga till en program inställning genom att följa ovanstående process.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Skärm bild av Azure Portal: listan meddelanden från val av Bell-formad ikon i portalens översta fält. Det finns ett meddelande om att inställningarna för webbapp har uppdaterats.":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du följt stegen för att ställa in en Function-app i Azure för användning med Azure Digital-dubbla.

Sedan kan du se hur du bygger på din grundläggande funktion för att mata in IoT Hub data i Azure Digitals:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)
