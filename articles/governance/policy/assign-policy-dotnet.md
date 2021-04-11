---
title: 'Snabb start: ny princip tilldelning med .NET Core'
description: I den här snabb starten använder du .NET Core för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: b89a0db419b1e7213e9742e4a39d073445d05189
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091794"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med .NET Core

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar kommer du att identifiera virtuella datorer som _inte är kompatibla_.

.NET Core-biblioteket används för att hantera Azure-resurser. Den här guiden beskriver hur du använder .NET Core-biblioteket för Azure Policy för att skapa en princip tilldelning.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Ett huvud namn för Azure-tjänsten, inklusive _clientId_ och _clientSecret_. Om du inte har ett huvud namn för tjänsten som ska användas med Azure Policy eller vill skapa ett nytt, se [Azure-hanterings bibliotek för .net-autentisering](/dotnet/azure/sdk/authentication#mgmt-auth).
  Hoppa över steget för att installera .NET Core-paketen som vi gör i nästa steg.

## <a name="create-the-azure-policy-project"></a>Skapa Azure Policy-projektet

Om du vill aktivera .NET Core för att hantera Azure Policy skapar du ett nytt konsol program och installerar de nödvändiga paketen.

1. Kontrol lera att den senaste .NET Core är installerad (minst **3.1.8**). Om den inte har installerats än kan du ladda ned den på [dotNet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initiera ett nytt .NET Core-konsolprogram med namnet "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Ändra kataloger till den nya projektmappen och installera de nödvändiga paketen för Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Bygg och publicera `policyAssignment` konsol programmet:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

1. Ändra kataloger till den `{run-folder}` som du definierade med föregående `dotnet publish` kommando.

1. Ange följande kommando i terminalen:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Föregående kommandon använder följande information:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{clientId}` -Ersätt med klient-ID: t för tjänstens huvud namn
- `{clientSecret}` -Ersätt med klient hemligheten för tjänstens huvud namn
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID
- **namn** – det unika namnet för princip tilldelnings objektet. Exemplet ovan använder _audit-VM-manageddisks_.
- **DisplayName** – visnings namn för princip tilldelningen. I det här fallet använder du _granskning av virtuella datorer utan Managed disks tilldelning_.
- **policyDefID** – princip definitions Sök vägen, baserat på vilken du använder för att skapa tilldelningen. I det här fallet är det ID: t för granskning av princip definition för _virtuella datorer som inte använder hanterade diskar_.
- **Beskrivning** – en djupare förklaring av vad principen gör eller varför den är tilldelad till det här omfånget.
- **omfång** – en omfattning avgör vilka resurser eller grupper av resurser som princip tilldelningen tillämpas på. Det kan vara ett intervall från en hanterings grupp till en enskild resurs. Se till att ersätta `{scope}` med något av följande mönster:
  - Hanterings grupp: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Prenumeration: `/subscriptions/{subscriptionId}`
  - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Klusterresursen `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Nu när din princip tilldelning har skapats kan du identifiera resurser som inte är kompatibla.

1. Initiera ett nytt .NET Core-konsolprogram med namnet "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Ändra kataloger till den nya projektmappen och installera de nödvändiga paketen för Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Bygg och publicera `policyAssignment` konsol programmet:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Ändra kataloger till den `{run-folder}` som du definierade med föregående `dotnet publish` kommando.

1. Ange följande kommando i terminalen:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Föregående kommandon använder följande information:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{clientId}` -Ersätt med klient-ID: t för tjänstens huvud namn
- `{clientSecret}` -Ersätt med klient hemligheten för tjänstens huvud namn
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID
- **namn** – det unika namnet för princip tilldelnings objektet. Exemplet ovan använder _audit-VM-manageddisks_.

Resultaten i `response` matchar det du ser på fliken **Resource Compliance (resurs krav** ) i en princip tilldelning i vyn Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

- Ta bort princip tilldelningen _Granska virtuella datorer utan Managed disks tilldelning_ via portalen. Princip definitionen är en inbyggd, så det finns ingen definition att ta bort.

- Om du vill ta bort program i .NET Core-konsolen och installerade paket tar du bort- `policyAssignment` och- `policyCompliance` projektfilerna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill veta mer om att tilldela princip definitioner för att verifiera att nya resurser är kompatibla fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)
