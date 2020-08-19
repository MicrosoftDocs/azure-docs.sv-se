---
title: Konfigurera en Azure-funktion för bearbetning av data
titleSuffix: Azure Digital Twins
description: Se hur du skapar en Azure-funktion som kan komma åt och utlösas av digitala dubbla.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d9f9957209c6df91185059085f57636a16a3961c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589409"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Anslut Azure Functions appar för bearbetning av data

Under för hands versionen hanteras uppdatering av digitala dubbla objekt baserat på data med hjälp av [**händelse vägar**](concepts-route-events.md) genom beräknings resurser, t. ex. [Azure Functions](../azure-functions/functions-overview.md). En Azure-funktion kan användas för att uppdatera en digital, som svar på:
* data från enheten som kommer från IoT Hub
* egenskaps ändring eller andra data som kommer från ett annat digitalt värde i den dubbla grafen

Den här artikeln vägleder dig genom att skapa en Azure-funktion för användning med Azure Digitals, dubbla. 

Här är en översikt över de steg som den innehåller:

1. Skapa en Azure Functions-app i Visual Studio
2. Skriv en Azure-funktion med en [Event Grid](../event-grid/overview.md) -utlösare
3. Lägg till en autentiseringsnyckel till funktionen (för att kunna komma åt Azure Digital-dubbla)
4. Publicera Function-appen till Azure
5. Konfigurera [säkerhets](concepts-security.md) åtkomst. För att Azure-funktionen ska kunna ta emot en åtkomsttoken vid körning för att komma åt tjänsten måste du konfigurera Hanterad tjänstidentitet för Function-appen.

Resten av den här artikeln vägleder dig genom stegen i Azure Function-installationen, en i taget.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Skapa en Azure Functions-app i Visual Studio

I Visual Studio 2019 väljer du *fil > nytt projekt*. Sök efter *Azure Functions* mall, markera den och tryck på Nästa.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: dialog rutan nytt projekt":::

Ange ett namn för Function-appen och tryck på "skapa".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: Konfigurera projekt dialog ruta":::

Välj *utlösaren Event Grid* och tryck på "skapa".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: dialog rutan utlösare för Azure Function":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Skriv en Azure-funktion med en Event Grid-utlösare

Följande kod skapar en kort Azure-funktion som du kan använda för att logga händelser: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Det här är din grundläggande Azure-funktion.

### <a name="run-and-debug-the-azure-function-app"></a>Köra och felsöka Azure Function-appen

Nu kan du kompilera och köra funktionen. Azure Functions är i slut ändan avsedda att köras i molnet, men du kan också köra och felsöka Azure Functions lokalt.

Mer information om detta finns i [*felsöka Event Grid utlösare lokalt*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Lägg till Azures digitala dubbla SDK i Azure Function-appen

Function-appen interagerar med Azure Digitals sammanflätade med [Azure IoT Digital-klient biblioteket för .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). För att kunna använda SDK måste du inkludera följande paket i projektet:
* `Azure.DigitalTwins.Core` (version `1.0.0-preview.2` )
* `Azure.Identity` (version `1.1.1` )

För att konfigurationen av Azure SDK-pipeline ska kunna konfigureras korrekt för Azure Functions, behöver du också:
* `Azure.Net.Http`
* `Azure.Core`

Beroende på vilka verktyg du väljer kan du göra det med Visual Studio Package Manager eller `dotnet` kommando rads verktyget. 

Lägg till följande using-uttryck i din Azure-funktion.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Lägg till autentiserings kod i Azure-funktionen

Lägg sedan till den kod som ska användas för att få åtkomst till Azure Digital-dubbla.

Lägg till variabler i funktions klassen för dessa värden: 
* ID för Azure Digitals dubbla appar
* URL: en för din Azure Digital-instansen. Det är en bra idé att läsa tjänste-URL: en från en miljö variabel, i stället för att hårdkoda den i-funktionen.
* En statisk variabel som innehåller en HttpClient-instans. HttpClient är relativt dyrt att skapa och vi vill undvika att behöva göra detta för varje funktions anrop.

Du kan också lägga till en lokal variabel i din funktion för att hålla din Azure Digitals dubbla klient instans till Function-projektet. Gör *inte* detta till en statisk variabel i klassen.

Till sist måste du ändra funktions signaturen så att den blir asynkron.

Efter dessa ändringar bör funktions koden likna följande:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

För att din functions-app ska kunna komma åt Azures digitala dubbla, måste den ha en Systemhanterad identitet och ha behörighet att komma åt Azure Digitals-instansen.

Använd följande kommando för att skapa den systemhanterade identiteten. Anteckna fältet *principalId* i utdata.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Använd värdet *principalId* i följande kommando för att tilldela Function-appens identitet till *ägar* rollen för din Azure Digital-instansen:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Mer information om hanterad identitet finns i [*använda hanterade identiteter för app service och Azure Functions*](../app-service/overview-managed-identity.md).

Slutligen kan du göra webb adressen till din Azure Digital-instansen tillgänglig för din funktion genom att ange en miljö variabel. Mer information finns i [*miljövariabler*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet*för Azure Digital-instansen. Om du vill se värd namnet, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publicera Azure Function-appen

Om du vill publicera Function-appen till Azure högerklickar du på funktions projektet (inte lösningen) i Solution Explorer och väljer *Publicera ()*.

Följande flik visas:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: dialog rutan publicera funktion, sidan 1":::

Välj eller skapa en App Service plan som ska användas med Azure Functions. Om du är osäker börjar du med att använda standard förbruknings planen.

> [!IMPORTANT] 
> Att publicera en Azure-funktion debiteras ytterligare avgifter för din prenumeration, oberoende av Azures digitala dubbla.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: dialog rutan publicera funktion, Sidan 2":::

På följande sida anger du det önskade namnet för den nya Function-appen, en resurs grupp och annan information.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Konfigurera säkerhets åtkomst för Azure Function-appen

Azure Function Skeleton från tidigare exempel kräver att en Bearer-token skickas till den för att kunna autentisera med Azure Digital-dubbla. För att säkerställa att denna Bearer-token skickas måste du konfigurera [hanterad tjänstidentitet (MSI)](../active-directory/managed-identities-azure-resources/overview.md) för Function-appen. Detta måste bara göras en gång för varje Function-app.

Om du vill konfigurera detta går du till [Azure Portal](https://portal.azure.com/) och navigerar till din Function-app.

På fliken *plattforms funktioner* väljer du *identitet*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: välja identitet för en Azure-funktion":::

På sidan identitet anger du *status* växla till *på*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: aktiverar identitets status":::

Observera också det **objekt-ID** som visas på den här sidan, eftersom det kommer att användas i nästa avsnitt.

### <a name="assign-access-roles"></a>Tilldela åtkomst roller

Eftersom Azure Digitals flätar använder rollbaserad åtkomst kontroll för att hantera åtkomst (se [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md) för mer information om detta) måste du också lägga till en roll för varje Function-app som du vill ge åtkomst till Azure Digital-objekt.

Om du vill tilldela en roll behöver du **resurs-ID** för den Azure Digital-instans som du har skapat. Om du inte har använt den tidigare när du skapade din instans kan du hämta den med hjälp av följande kommando:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
Resurs-ID är en del av utdata, som en lång sträng med namnet "ID" som börjar med bokstaven "/Subscriptions/...".

Använd resurs-ID tillsammans med Azure Functions objekt-ID från tidigare i kommandot nedan:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du följt stegen för att ställa in en Azure-funktion för användning med Azure Digitals. Sedan kan du prenumerera på Azure-funktionen till Event Grid för att lyssna på en slut punkt. Den här slut punkten kan vara:
* En Event Grid slut punkt som är ansluten till Azure Digitals, för att bearbeta meddelanden som kommer från Azure Digitals fristående (t. ex. egenskaps ändrings meddelanden, telemetri som genererats av [digitala](concepts-twins-graph.md) enheter i den dubbla grafen eller livs cykel meddelanden)
* De IoT system-ämnen som används av IoT Hub för att skicka telemetri och andra enhets händelser
* En Event Grid-slutpunkt tar emot meddelanden från andra tjänster

Sedan kan du se hur du bygger på din grundläggande Azure-funktion för att mata in IoT Hub data i Azure Digitals:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)