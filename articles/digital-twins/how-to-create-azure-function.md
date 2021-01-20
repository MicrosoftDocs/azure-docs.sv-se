---
title: Konfigurera en funktion i Azure för bearbetning av data
titleSuffix: Azure Digital Twins
description: Se hur du skapar en funktion i Azure som kan komma åt och utlösas av digitala dubbla.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6f74f973abc33d809624bd8abd5a514a52ccfe70
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602707"
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

Välj _fil > nytt > projekt_ i Visual Studio 2019 och sök efter _Azure Functions_ -mallen genom att välja _Nästa_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: dialog rutan nytt projekt":::

Ange ett namn för Function-appen och välj _skapa_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: Konfigurera nytt projekt":::

Välj typen av Function-appen *Event Grid utlösare* och välj _skapa_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Azure Functions projekt utlösare, dialog ruta":::

När din Function-app har skapats, kommer Visual Studio att ha automatiskt ifyllt kod exempel i **Function.cs** -filen i projektmappen. Den här korta funktionen används för att logga händelser.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: projekt fönster med exempel kod":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Skriv en funktion med en Event Grid-utlösare

Du kan skriva en funktion genom att lägga till SDK i din Function-app. Function-appen interagerar med Azure Digitals dubbla med [Azure Digitals-SDK för .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

För att kunna använda SDK måste du inkludera följande paket i projektet. Du kan antingen installera paketen med hjälp av Visual Studio NuGet Package Manager eller lägga till paketen med hjälp av `dotnet` kommando rads verktyget. Välj någon av följande metoder: 

**Alternativ 1. Lägg till paket med hjälp av Visual Studio Package Manager:**
    
Du kan göra detta genom att högerklicka på projektet och välja _Hantera NuGet-paket_ i listan. Sedan väljer du fliken _Bläddra_ i fönstret som öppnas och söker efter följande paket. Välj _Installera_ och _Godkänn_ licens avtalet för att installera paketen.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Alternativ 2. Lägg till paket med `dotnet` kommando rads verktyget:**

Du kan också använda följande `dotnet add` kommandon i ett kommando rads verktyg:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Öppna sedan _Function.cs_ -filen i Visual Studio-Solution Explorer där du har exempel kod och Lägg till följande _using_ -instruktioner till din funktion. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Lägg till autentiserings kod till funktionen

Du kommer nu att deklarera variabler på klass nivå och lägga till en kod som gör det möjligt att komma åt Azure Digitals dubbla. Du kommer att lägga till följande i din funktion i filen {your Function Name}. cs.

* Läs ADT-tjänstens URL som en miljö variabel. Det är en bra idé att läsa tjänste-URL: en från en miljö variabel, i stället för att hårdkoda den i-funktionen.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* En statisk variabel som innehåller en HttpClient-instans. HttpClient är relativt dyrt att skapa och vi vill undvika att behöva göra detta för varje funktions anrop.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Du kan använda autentiseringsuppgifterna för hanterad identitet i Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Lägg till en lokal variabel som _DigitalTwinsClient_ i din funktion för att hålla din Azure Digital-klient instans till Function-projektet. Gör *inte* den här variabeln statisk i klassen.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Lägg till en null-kontroll för _adtInstanceUrl_ och packa upp funktions logiken i ett try catch-block för att fånga eventuella undantag.

Efter dessa ändringar ser funktions koden ut ungefär så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>Publicera funktionsappen till Azure

Om du vill publicera projektet i en Function-app i Azure, högerklickar du på funktions projektet (inte lösningen) i Solution Explorer och väljer **publicera**.

> [!IMPORTANT] 
> Om du publicerar till en Function-app i Azure debiteras ytterligare avgifter för din prenumeration, oberoende av Azures digitala dubbla.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: publicera funktion till Azure":::

Välj **Azure** som publicerings mål och välj **Nästa**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: dialog rutan publicera Azure Functions, välj Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: dialog rutan publicera funktion, välj Azure-Funktionsapp (Windows) eller (Linux) baserat på din dator":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: dialog rutan publicera funktion, skapa en ny Azure Function":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: dialog rutan publicera funktion, Fyll i fälten och välj Skapa":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: dialog rutan publicera funktion, välj din Function-app i listan och slutför":::

På följande sida anger du det önskade namnet för den nya Function-appen, en resurs grupp och annan information.
För att din Function-app ska kunna komma åt Azures digitala dubbla, måste den ha en Systemhanterad identitet och ha behörighet att komma åt din Azure Digital-instansen.

Sedan kan du ställa in säkerhets åtkomst för funktionen med CLI eller Azure Portal. Välj någon av följande metoder:

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurera säkerhets åtkomst för Function-appen
Du kan ställa in säkerhets åtkomst för Function-appen med något av följande alternativ:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Alternativ 1: Konfigurera säkerhets åtkomst för Function-appen med CLI

Funktionen Skeleton från tidigare exempel kräver att en Bearer-token skickas till den, för att kunna autentisera med Azure Digital-dubbla. För att säkerställa att denna Bearer-token skickas måste du konfigurera [hanterad tjänstidentitet (MSI)](../active-directory/managed-identities-azure-resources/overview.md) för Function-appen. Detta måste bara göras en gång för varje Function-app.

Du kan skapa Systemhanterad identitet och tilldela Function-appen identitet till den _**digitala Azure-dataägarens data ägar**_ roll för Azure Digitals-instansen. Detta ger funktionen app behörighet i instansen för att utföra data Plans aktiviteter. Sedan kan du göra webb adressen till Azure Digitals dubblare-instans tillgänglig för din funktion genom att ange en miljö variabel.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Använd [Azure Cloud Shell](https://shell.azure.com) för att köra kommandona.

Använd följande kommando för att skapa den systemhanterade identiteten. Anteckna fältet _principalId_ i utdata.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Använd _principalId_-värdet i följande kommando för att tilldela funktionsappens identitet till _Azure Digital Twins-dataägarrollen_ för Azure Digital-instansen.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Slutligen kan du göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion genom att ange en miljö variabel. Mer information om hur du ställer in miljövariabler finns i [*miljövariabler*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet* för Azure Digital-instansen. Om du vill se värd namnet, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Alternativ 2: Konfigurera säkerhets åtkomst för Function-appen med hjälp av Azure Portal

En systemtilldelad hanterad identitet gör det möjligt för Azure-resurser att autentisera till moln tjänster (till exempel Azure Key Vault) utan att lagra autentiseringsuppgifter i kod. När den är aktive rad kan alla nödvändiga behörigheter beviljas via rollbaserad åtkomst kontroll i Azure. Livs cykeln för den här typen av hanterad identitet är kopplad till livs cykeln för den här resursen. Dessutom kan varje resurs (till exempel virtuell dator) bara ha en systemtilldelad hanterad identitet.

I [Azure Portal](https://portal.azure.com/)söker du efter _Function-appen_ i Sök fältet med namnet på den funktion som du skapade tidigare. Välj *Funktionsapp* i listan. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: Sök funktionens app":::

I fönstret Function-app väljer du _identitet_ i navigerings fältet till vänster för att aktivera hanterad identitet.
Under fliken _tilldelade system_ växlar du _statusen_ till på och _sparar_ den. Du kommer att se ett popup-fönster för att _Aktivera systemtilldelad hanterad identitet_.
Välj _Ja_ -knapp. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: Aktivera Systemhanterad identitet":::

Du kan kontrol lera i meddelandena att din funktion har registrerats med Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: meddelanden":::

Observera också det **objekt-ID** som visas på sidan _identitet_ , eftersom det kommer att användas i nästa avsnitt.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Kopiera objekt-ID: t som ska användas i framtiden":::

### <a name="assign-access-roles-using-azure-portal"></a>Tilldela åtkomst roller med Azure Portal

Välj knappen _Azure Role-tilldelningar_ som öppnar sidan *roll tilldelningar för Azure* . Välj sedan _+ Lägg till roll tilldelning (för hands version)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: Lägg till roll tilldelning":::

På sidan _Lägg till roll tilldelning (förhands granskning)_ som öppnas väljer du:

* _Omfång_: Resursgrupp
* _Prenumeration_: Välj din Azure-prenumeration
* _Resurs grupp_: Välj din resurs grupp i list rutan
* _Roll_: Välj _Azure Digitals sammanflätade data ägare_ i list rutan

Spara sedan informationen genom att trycka på knappen _Spara_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: Lägg till roll tilldelning (för hands version) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurera program inställningar med Azure Portal

Du kan göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion genom att ange en miljö variabel. Mer information finns i [*miljövariabler*](/sandbox/functions-recipes/environment-variables). Program inställningarna visas som miljövariabler för att få åtkomst till den digitala dubbla instansen. 

Du behöver ADT_INSTANCE_URL för att skapa en program inställning.

Du kan få ADT_INSTANCE_URL genom att lägga till **_https://_** till ditt instans värd namn. I Azure Portal kan du hitta värd namnet för den digitala dubbla instansen genom att söka efter din instans i Sök fältet. Välj sedan _Översikt_ i det vänstra navigerings fältet för att visa _värd namnet_. Kopiera det här värdet om du vill skapa en program inställning.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: översikt – > kopiera värdnamn som ska användas i fältet _Value_.":::

Nu kan du skapa en program inställning genom att följa stegen nedan:

* Sök efter din app med hjälp av namnet på appens funktion i Sök fältet och välj Function-appen i listan
* Välj _konfiguration_ i navigerings fältet till vänster för att skapa en ny program inställning
* På fliken _program inställningar_ väljer du _+ ny program inställning_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: Sök efter en befintlig Function-app":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: Konfigurera program inställningar":::

I fönstret som öppnas, använder du värdet som kopierades ovan för att skapa en program inställning. \
_Namn_  : ADT_SERVICE_URL \
_Värde_ : https://{Your-Azure-Digital-flätas-hostname}

Välj _OK_ för att skapa en program inställning.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: Lägg till program inställningar.":::

Du kan visa dina program inställningar med program namn under fältet _namn_ . Spara sedan program inställningarna genom att välja knappen _Spara_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: Visa programmet som skapats och starta om programmet":::

Alla ändringar av program inställningarna behöver starta om programmet. Välj _Fortsätt_ för att starta om programmet.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: Spara program inställningar":::

Du kan visa att program inställningarna uppdateras genom att välja _meddelande_ ikon. Om program inställningen inte skapas kan du försöka lägga till en program inställning genom att följa ovanstående process.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: meddelanden om att uppdatera program inställningar":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du följt stegen för att ställa in en Function-app i Azure för användning med Azure Digital-dubbla. Sedan kan du prenumerera på din funktion till Event Grid för att lyssna på en slut punkt. Den här slut punkten kan vara:
* En Event Grid slut punkt som är ansluten till Azure Digitals, för att bearbeta meddelanden som kommer från Azure Digitals fristående (t. ex. egenskaps ändrings meddelanden, telemetri som genererats av [digitala](concepts-twins-graph.md) enheter i den dubbla grafen eller livs cykel meddelanden)
* De IoT system-ämnen som används av IoT Hub för att skicka telemetri och andra enhets händelser
* En Event Grid-slutpunkt tar emot meddelanden från andra tjänster

Sedan kan du se hur du bygger på din grundläggande funktion för att mata in IoT Hub data i Azure Digitals:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)
