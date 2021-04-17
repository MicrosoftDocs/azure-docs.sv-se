---
title: Skriva appautentiseringskod
titleSuffix: Azure Digital Twins
description: Se hur du skriver autentiseringskod i ett klientprogram
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589404"
---
# <a name="write-client-app-authentication-code"></a>Skriva autentiseringskod för klientapp

När du [har skapat en Azure Digital Twins-instans och](how-to-set-up-instance-portal.md)autentisering kan du skapa ett klientprogram som du använder för att interagera med instansen. När du har ställt in ett startklientprojekt  måste du skriva kod i klientappen för att autentisera den mot Azure Digital Twins instansen.

Azure Digital Twins utför autentisering med hjälp av [Azure AD-säkerhetstoken baserat på OAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). För att autentisera din SDK måste du hämta en bearer-token med rätt behörigheter för att Azure Digital Twins och skicka den tillsammans med dina API-anrop. 

I den här artikeln beskrivs hur du hämtar autentiseringsuppgifter med `Azure.Identity` hjälp av klientbiblioteket. Den här artikeln visar kodexempel i C#, till exempel vad du skriver för [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client)men du kan använda en version oavsett vilken SDK du använder (mer information om SDK:er som är tillgängliga för Azure Digital Twins finns i Så här gör du: Använda `Azure.Identity` [*Azure Digital Twins-API:er*](how-to-use-apis-sdks.md)och SDK:er ).

## <a name="prerequisites"></a>Förutsättningar

Slutför först installationsstegen i [*Anvisningar: Konfigurera en instans och autentisering.*](how-to-set-up-instance-portal.md) Detta säkerställer att du har en Azure Digital Twins instans och att användaren har åtkomstbehörigheter. Efter installationen är du redo att skriva kod för klientappen.

För att fortsätta behöver du ett klientappsprojekt där du skriver koden. Om du inte redan har skapat ett klientappsprojekt skapar du ett grundläggande projekt på det språk du väljer att använda med den här självstudien.

## <a name="common-authentication-methods-with-azureidentity"></a>Vanliga autentiseringsmetoder med Azure.Identity

`Azure.Identity` är ett klientbibliotek som tillhandahåller flera metoder för att hämta autentiseringsuppgifter som du kan använda för att hämta en bearer-token och autentisera med din SDK. Även om den här artikeln innehåller exempel i C# kan du `Azure.Identity` visa för flera språk, inklusive...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Tre vanliga metoder för att hämta autentiseringsuppgifter `Azure.Identity` i är:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) tillhandahåller ett standardautentiseringsflöde för program som distribueras till Azure och är det rekommenderade `TokenCredential` valet för lokal **utveckling.** Det kan också aktiveras för att prova de andra två metoderna som rekommenderas i den här artikeln. den omsluter `ManagedIdentityCredential` och kan komma åt med en `InteractiveBrowserCredential` konfigurationsvariabel.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) fungerar bra i fall där du behöver hanterade identiteter [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)och är en bra kandidat för att arbeta med Azure Functions och distribuera till Azure-tjänster.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) är avsett för interaktiva program och kan användas för att skapa en autentiserad SDK-klient

I följande exempel visas hur du använder var och en av dessa med .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Autentiseringsexempel: .NET (C#) SDK

Det här avsnittet visar ett exempel i C# för att använda den angivna .NET SDK:n för att skriva autentiseringskod.

Ta först med `Azure.DigitalTwins.Core` SDK-paketet och `Azure.Identity` paketet i projektet. Beroende på vilka verktyg du väljer kan du inkludera paketen med hjälp Visual Studio-pakethanteraren `dotnet` eller kommandoradsverktyget. 

Du måste också lägga till följande using-instruktioner i projektkoden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Lägg sedan till kod för att hämta autentiseringsuppgifter med någon av metoderna i `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential-metod

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) tillhandahåller ett standardautentiseringsflöde för program som distribueras till Azure och är det rekommenderade `TokenCredential` valet för lokal **utveckling.**

Om du vill använda Azure-standardautentiseringsuppgifterna behöver du Azure Digital Twins-instansens URL ([instruktioner för att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Här är ett kodexempel för att lägga till `DefaultAzureCredential` en i projektet:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala Azure-autentiseringsuppgifter

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential-metod

Metoden [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) fungerar bra i fall där du behöver hanterade identiteter [(MSI),](../active-directory/managed-identities-azure-resources/overview.md)till exempel när du arbetar med Azure Functions.

Det innebär att du kan `ManagedIdentityCredential` använda i samma projekt som eller , för att autentisera en annan del av `DefaultAzureCredential` `InteractiveBrowserCredential` projektet.

Om du vill använda standardautentiseringsuppgifterna för Azure Azure Digital Twins-instansens URL ([instruktioner för att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

I en Azure-funktion kan du använda autentiseringsuppgifterna för hanterade identiteter så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential-metod

Metoden [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) är avsedd för interaktiva program och ger en webbläsare för autentisering. Du kan använda detta i stället `DefaultAzureCredential` för i fall där du behöver interaktiv autentisering.

Om du vill använda autentiseringsuppgifterna för den interaktiva webbläsaren behöver du **en appregistrering** som har behörighet till Azure Digital Twins API:er. Anvisningar om hur du ställer in den här appregistreringen [*finns i Anvisningar: Skapa en appregistrering*](how-to-create-app-registration.md). När appregistreringen har ställts in behöver du...
* appregistreringens *program-ID (klient)* ([instruktioner för att hitta](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* appregistreringens *katalog-ID (klient)* ([instruktioner för att hitta](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* den Azure Digital Twins-instansens URL ([instruktioner för att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Här är ett exempel på koden för att skapa en autentiserad SDK-klient med hjälp av `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Även om du kan placera klient-ID, klient-ID och instans-URL direkt i koden som visas ovan, är det en bra idé att din kod hämtar dessa värden från en konfigurationsfil eller miljövariabel i stället.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Andra kommentarer om att autentisera Azure Functions

Ett mer komplett exempel som förklarar några av de viktiga konfigurationsalternativen i kontexten för funktioner finns i Konfigurera en [*Azure-funktion*](how-to-create-azure-function.md) för bearbetning av data.

Om du vill använda autentisering i en funktion måste du också komma ihåg att:
* [Aktivera hanterad identitet](../app-service/overview-managed-identity.md?tabs=dotnet)
* Använda [miljövariabler](/sandbox/functions-recipes/environment-variables?tabs=csharp) efter behov
* Tilldela behörigheter till functions-appen som gör att den kan komma åt Digital Twins API:er. Mer information om Azure Functions finns i [*How-to: Set up an Azure function for processing data*](how-to-create-azure-function.md).

## <a name="authenticate-across-tenants"></a>Autentisera mellan klienter

Azure Digital Twins är en tjänst som endast stöder  [en Azure Active Directory-klientorganisation (Azure AD):](../active-directory/develop/quickstart-create-new-tenant.md)den huvudsakliga klientorganisationen från den prenumeration där Azure Digital Twins-instansen finns.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Om du behöver åtkomst till din Azure Digital Twins-instans med hjälp av ett huvudnamn för tjänsten eller ett användarkonto som tillhör en annan klientorganisation än instansen kan du få varje federerad identitet från en annan klientorganisation att begära en **token** från Azure Digital Twins-instansens "hem"-klientorganisation. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

Du kan också ange hemklientorganisationen i autentiseringsalternativen i koden. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Andra autentiseringsmetoder

Om de markerade autentiseringsscenarierna ovan inte täcker behoven i din app kan du utforska andra typer av autentisering som erbjuds i [**Microsoft Identity Platform.**](../active-directory/develop/v2-overview.md#getting-started) Dokumentationen för den här plattformen omfattar ytterligare autentiseringsscenarier, ordnade efter programtyp.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur säkerhet fungerar i Azure Digital Twins:
* [*Koncept: Säkerhet för Azure Digital Twins lösningar*](concepts-security.md)

Eller, nu när autentiseringen har ställts in, går du vidare till att skapa och hantera modeller i din instans:
* [*Så här hanterar du DTDL-modeller*](how-to-manage-model.md)