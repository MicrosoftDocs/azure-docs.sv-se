---
title: Skriva appautentiseringskod
titleSuffix: Azure Digital Twins
description: Se så här skriver du en autentiseringsnyckel i ett klient program
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 008d5f22a48fdd31c90e63643adc94b26a975ca2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589375"
---
# <a name="write-client-app-authentication-code"></a>Skriv kod för klientautentisering för klient program

När du [har konfigurerat en Azure Digital-instans och-autentisering](how-to-set-up-instance-scripted.md)kan du skapa ett klient program som du använder för att interagera med instansen. När du har konfigurerat ett start klient projekt visar den här artikeln hur du **skriver kod i klient programmet för att autentisera den** mot Azure Digitals-instansen.

Det finns två sätt att exempel kod i den här artikeln. Du kan använda den som passar dig bäst, beroende på vilket språk du väljer:
* I det första avsnittet i exempel koden används Azure Digitals .NET-SDK (C#). SDK är en del av Azure SDK för .NET och finns här: [*Azure IoT Digital-klient bibliotek för .net*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* Det andra avsnittet i exempel koden är för användare som inte använder .NET SDK och i stället använda AutoRest-genererade SDK: er på andra språk. Mer information om den här strategin finns i [*instruktion: skapa anpassade SDK: er för Azure Digitals med AutoRest*](how-to-create-custom-sdks.md).

Du kan också läsa mer om API: er och SDK: er för Azure Digitals i [*anvisningar: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Förutsättningar

Slutför först installations stegen i [*anvisningar: Konfigurera en instans och autentisering*](how-to-set-up-instance-scripted.md). På så sätt ser du till att du har en Azure Digitals-instans, att användaren har åtkomst behörighet och att du har konfigurerat behörigheter för klient program. När du har konfigurerat den här konfigurationen är du redo att skriva kod för klient program.

Om du vill fortsätta måste du ha ett klient-app-projekt där du skriver koden. Om du inte redan har skapat ett klient-app-projekt skapar du ett Basic-projekt på det språk som du väljer att använda med den här självstudien.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Autentisering och klient skapande: .NET (C#) SDK

Ta först med följande paket i projektet för att använda .NET SDK och autentiseringsinställningar för den här instruktionen:
* `Azure.DigitalTwins.Core` (version `1.0.0-preview.2` )
* `Azure.Identity` (version `1.1.1` )

Beroende på vilka verktyg du väljer kan du inkludera paketen med hjälp av Visual Studio Package Manager eller `dotnet` kommando rads verktyget. 

Om du vill autentisera med .NET SDK använder du en av de metoder för autentisering som har definierats i [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -biblioteket.

Här är två som används ofta: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Den här metoden är avsedd för interaktiva program och kommer att hämta en webbläsare för autentisering.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Den här metoden fungerar bra i de fall där du behöver [hanterade identiteter (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), till exempel när du arbetar med Azure Functions. 

Du behöver också följande using-instruktioner:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Om du vill använda de interaktiva webb läsar autentiseringsuppgifterna för att skapa en autentiserad SDK-klient lägger du till följande kod:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Även om du kan placera klient-ID, klient-ID och instans-URL direkt i koden som visas ovan, är det en bra idé att låta koden hämta dessa värden från en konfigurations fil eller en miljö variabel i stället.

I en Azure-funktion kan du sedan använda autentiseringsuppgifterna för hanterad identitet som detta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Se [*anvisningar: Konfigurera en Azure-funktion för att bearbeta data*](how-to-create-azure-function.md) för ett mer komplett exempel som förklarar några av de viktiga konfigurations alternativen i kontexten för functions.

Om du vill använda autentisering i en funktion måste du också komma ihåg att:
* [Aktivera hanterad identitet](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Använd [miljövariabler](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) efter behov
* Tilldela behörigheter till Functions-appen som möjliggör åtkomst till digitala dubbla API: er. Mer information om Azure Functions-processer finns i [*instruktion: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Autentisering med en AutoRest-genererad SDK

Om du inte använder .NET kan du välja att skapa ett SDK-bibliotek på ett valfritt språk, enligt beskrivningen i [*instruktion: skapa anpassade SDK: er för Azure Digitals med AutoRest*](how-to-create-custom-sdks.md).

I det här avsnittet beskrivs hur du autentiserar i så fall.

### <a name="prerequisites"></a>Förutsättningar

Först bör du slutföra stegen för att skapa en anpassad SDK med AutoRest med hjälp av stegen i [*instruktion: skapa anpassade SDK: er för Azure Digitals med AutoRest*](how-to-create-custom-sdks.md).

I det här exemplet används en typescript-SDK som genereras med AutoRest. Därför kräver det också:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Minimal kod exempel för autentisering

Om du vill autentisera en app med Azure-tjänster kan du använda följande minimala kod i klient programmet.

Du behöver ditt *program (klient) ID* och *katalog (klient) ID* från tidigare, samt URL: en för din Azure Digital-instansen.

> [!TIP]
> URL: en för Azure Digitals dubbla instanser görs genom att lägga till *https://* i början av *värd namnet*för Azure Digital-instansen. Om du vill se *värd namnet*, tillsammans med alla egenskaper för din instans, kan du köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . Du kan använda `az account show --query tenantId` kommandot för att se din *katalog (klient)-ID*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Observera att om koden ovan placerar klient-ID, klient-ID och instans-URL direkt i koden för enkelhetens skull är det en bra idé att låta koden hämta de här värdena från en konfigurations fil eller en miljö variabel i stället.

MSAL har många fler alternativ som du kan använda för att implementera saker som cachelagring och andra autentiserings flöden. Mer information om detta finns i [*Översikt över Microsoft Authentication Library (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur säkerheten fungerar i Azure Digitals:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)

Eller, nu när autentiseringen har kon figurer ATS, går du vidare till att skapa modeller i din instans:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)