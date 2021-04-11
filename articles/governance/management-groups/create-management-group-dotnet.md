---
title: 'Snabb start: skapa en hanterings grupp med .NET Core'
description: I den här snabb starten använder du .NET Core för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592593"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Snabb start: skapa en hanterings grupp med .NET Core

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Ett huvud namn för Azure-tjänsten, inklusive _clientId_ och _clientSecret_. Om du inte har ett huvud namn för tjänsten som ska användas med Azure Policy eller vill skapa ett nytt, se [Azure-hanterings bibliotek för .net-autentisering](/dotnet/azure/sdk/authentication#mgmt-auth).
  Hoppa över steget för att installera .NET Core-paketen som vi gör i nästa steg.

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Programkonfiguration

Om du vill aktivera .NET Core för hantering av hanterings grupper skapar du ett nytt konsol program och installerar de nödvändiga paketen.

1. Kontrol lera att den senaste .NET Core är installerad (minst **3.1.8**). Om den inte har installerats än kan du ladda ned den på [dotNet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initiera ett nytt .NET Core-konsolprogram med namnet "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Ändra kataloger till den nya projektmappen och installera de nödvändiga paketen för Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Ersätt standardvärdet `program.cs` med följande kod och spara den uppdaterade filen:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Bygg och publicera `mgCreate` konsol programmet:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Skapa hanterings gruppen

I den här snabb starten skapar du en ny hanterings grupp i rot hanterings gruppen.

1. Ändra kataloger till den `{run-folder}` som du definierade med föregående `dotnet publish` kommando.

1. Ange följande kommando i terminalen:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Föregående kommandon använder följande information:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{clientId}` -Ersätt med klient-ID: t för tjänstens huvud namn
- `{clientSecret}` -Ersätt med klient hemligheten för tjänstens huvud namn
- `{groupID}` -Ersätt med ID: t för den nya hanterings gruppen
- `{displayName}` – Ersätt med det egna namnet på den nya hanterings gruppen

Resultatet är en ny hanterings grupp i rot hanterings gruppen.

## <a name="clean-up-resources"></a>Rensa resurser

- Ta bort den nya hanterings gruppen via portalen.

- Om du vill ta bort programmet för .NET Core-konsolen och installerade paket tar du bort `mgCreate` projektmappen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)