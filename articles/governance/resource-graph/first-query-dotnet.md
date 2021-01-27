---
title: 'Snabb start: din första .NET Core-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs diagram NuGet-paket för .NET Core och köra din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917579"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Snabb start: kör din första resurs diagram fråga med .NET Core

Det första steget för att använda Azure Resource Graph är att kontrol lera att de nödvändiga paketen för .NET Core är installerade. Den här snabb starten vägleder dig genom processen att lägga till paketen i .NET Core-installationen.

I slutet av den här processen har du lagt till paketen i .NET Core-installationen och kört din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Ett huvud namn för Azure-tjänsten, inklusive _clientId_ och _clientSecret_. Om du inte har ett huvud namn för tjänsten som ska användas med resurs diagram eller om du vill skapa en ny, se [Azure-hanterings bibliotek för .net-autentisering](/dotnet/azure/sdk/authentication#mgmt-auth).
  Hoppa över steget för att installera .NET Core-paketen som vi gör i nästa steg.

## <a name="create-the-resource-graph-project"></a>Skapa resurs diagrams projektet

Om du vill aktivera .NET Core för att fråga Azure Resource Graph skapar du ett nytt konsol program och installerar de nödvändiga paketen.

1. Kontrol lera att den senaste .NET Core är installerad (minst **3.1.5**). Om den inte har installerats än kan du ladda ned den på [dotNet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initiera ett nytt .NET Core-konsolprogram med namnet "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Ändra kataloger till den nya projektmappen och installera de nödvändiga paketen för Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

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
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Bygg och publicera `argQuery` konsol programmet:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Med .NET Core Console-programmet byggt och publicerat är det dags att testa en enkel resurs diagram fråga. Frågan returnerar de fem första Azure-resurserna med **namn** och **resurs typ** för varje resurs.

I varje anrop till finns `argQuery` det variabler som du måste ersätta med dina egna värden:

- `{tenantId}` – Ersätt med ditt klientorganisations-ID
- `{clientId}` -Ersätt med klient-ID: t för tjänstens huvud namn
- `{clientSecret}` -Ersätt med klient hemligheten för tjänstens huvud namn
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

1. Ändra kataloger till den `{run-folder}` som du definierade med föregående `dotnet publish` kommando.

1. Kör din första Azure Resource Graph-fråga med hjälp av det kompilerade .NET Core-konsol programmet:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Ändra den sista parametern till `argQuery.exe` och ändra frågan till `order by` egenskapen **namn** :

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Ändra den sista parametern till `argQuery.exe` och ändra frågan till först `order by` egenskapen **namn** och sedan `limit` till de fem främsta resultaten:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort programmet .NET Core Console och installerade paket kan du göra det genom att ta bort `argQuery` projektmappen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett .NET Core-konsolprogram med de nödvändiga resurs diagram paketen och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)