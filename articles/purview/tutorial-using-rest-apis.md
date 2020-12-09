---
title: 'Självstudie: använda REST-API: er'
description: 'I den här självstudien beskrivs hur du använder REST-API: er för Azure avdelningens kontroll för att komma åt innehållet i katalogen.'
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 7a2e029fbcc1a26da9c8c8cbff5ee3b9bbb849e0
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928281"
---
# <a name="tutorial-use-the-rest-apis"></a>Självstudie: använda REST-API: er

I den här självstudien får du lära dig hur du använder REST-API: er för Azure avdelningens kontroll. Alla som vill skicka data till en Azure avdelningens kontroll-katalog, inkludera katalogen som en del av en automatiserad process eller skapa en egen användar upplevelse i katalogen kan använda REST-API: er för att göra det.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa ett huvud namn för tjänsten (program).
> * Konfigurera din katalog att lita på tjänstens huvud namn (program).
> * Visa REST API-dokumentationen.
> * Samla in de nödvändiga värdena för att använda REST-API: er.
> * Använd Postman-klienten för att anropa REST-API: erna.
> * Generera en .NET-klient för att anropa REST-API: erna.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="prerequisites"></a>Krav

* Du måste ha ett befintligt Azure avdelningens kontroll-konto för att komma igång. Om du inte har någon katalog kan du läsa [snabb starten för att skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).

* Om du behöver lägga till data i din katalog går du [till självstudien för att köra start paketet och Genomsök data](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Skapa ett huvud namn för tjänsten (program)

För att en REST API-klient ska kunna få åtkomst till katalogen måste klienten ha ett huvud namn för tjänsten (program) och en identitet som katalogen känner igen och är konfigurerad att lita på. När du gör REST API anrop till katalogen använder de tjänstens huvud namn.

Kunder som har använt befintliga huvud namn för tjänsten (program-ID: n) har haft en hög frekvens av misslyckade. Vi rekommenderar därför att du skapar ett nytt huvud namn för tjänsten för att anropa API: er.

Så här skapar du ett nytt huvud namn för tjänsten:

1. Sök efter och välj **Azure Active Directory** från [Azure Portal](https://portal.azure.com).
1. Välj **Appregistreringar** i den vänstra rutan på sidan **Azure Active Directory** .
1. Välj **ny registrering**.
1. På sidan **Registrera ett program** :
    1. Ange ett **namn** för programmet (tjänstens huvud namn).
    1. Välj **konton endast i den här organisations katalogen (endast _&lt; klient organisationens namn &gt;_ – enskild klient)**.
    1. För **omdirigerings-URI (valfritt)** väljer du **webb** och anger ett värde. Det här värdet behöver inte vara en giltig URI.
    1. Välj **Register** (Registrera).
1. På sidan nytt huvud namn för tjänsten kopierar du värdena för **visnings namnet** och **program-ID: t (klienten)** som ska sparas senare.

   Program-ID är `client_id` värdet i exempel koden.

Om du vill använda tjänstens huvud namn (program) måste du hämta lösen ordet. Gör så här:

1. Sök efter och välj **Azure Active Directory** från Azure Portal och välj sedan **Appregistreringar** i det vänstra fönstret.
1. Välj tjänstens huvud namn (program) i listan.
1. Välj **certifikat & hemligheter** i det vänstra fönstret.
1. Välj **Ny klienthemlighet**.
1. På sidan **Lägg till en klient hemlighet** anger du en **Beskrivning**, väljer en förfallo tid under **upphör ande** och väljer sedan **Lägg till**.

   På sidan **klient hemligheter** är strängen i kolumnen **värde** i den nya hemligheten ditt lösen ord. Spara det här värdet.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Skärm bild som visar en klient hemlighet.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Konfigurera din katalog att lita på tjänstens huvud namn (program)

Så här konfigurerar du Azure-avdelningens kontroll att lita på ditt nya huvud namn för tjänsten:

1. Navigera till ditt avdelningens kontroll-konto

1. På sidan **avdelningens kontroll-konto** väljer du fliken **åtkomst kontroll (IAM)**

1. Klicka på **+ Lägg till**

1. Välj **Lägg till roll tilldelning**

1. För roll typen i **avdelningens kontroll data curator**

    > [!Note]
    > Mer information om Azure avdelningens kontroll Data Catalog-behörigheter finns i [katalog behörigheter](catalog-permissions.md). Om du till exempel behöver behörighet att aktivera skanning måste roll typen vara **avdelningens kontroll Data Source Administrator**.

1. För **tilldela behörighet att** lämna kvar standard-, **användar-, grupp-eller tjänstens huvud namn**

1. För **väljer** du ange namnet på användaren, Azure Active Directory grupp eller tjänstens huvud namn som du vill tilldela och klickar sedan på namnet i resultat fönstret.

1. Klicka på **Spara**

Du har nu konfigurerat tjänstens huvud namn som program administratör, vilket gör det möjligt att skicka innehåll till katalogen.

## <a name="view-the-rest-apis-documentation"></a>Visa REST API-dokumentationen

Om du vill visa API Swagger-dokumentationen laddar du ned [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), extraherar filerna och öppnar index.html.

Om du vill veta mer om den avancerade sökning/föreslå API som Azure avdelningens kontroll tillhandahåller kan du läsa mer i dokumentationen om REST API-sökfilter. Den AutoRest-genererade klienten stöder för närvarande inte anpassade Sök parametrar. Använd Sök filter dokumentet för att definiera filter klasser i koden som API-anrop parametrar för att lösa problemet. index.html-dokumentet innehåller exempel på dessa API: er.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Samla in de nödvändiga värdena för att använda REST-API: er

Hitta och spara följande värden:

* Klientorganisations-ID:
  * I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure Active Directory**.
  * I avsnittet **Hantera** i det vänstra fönstret väljer du **Egenskaper**, letar efter **klient-ID** och väljer sedan ikonen **Kopiera till Urklipp** för att spara dess värde.
* Atlas slut punkt:
  * På [sidan för Azure avdelningens kontroll-konton på sidan](https://aka.ms/purviewportal) Azure Portal letar du upp och väljer ditt Azure avdelningens kontroll-konto i listan.
  * Välj **Översikt**, hitta **Atlas slut punkt** och välj sedan ikonen **Kopiera till Urklipp** för att spara dess värde. Ta bort *https://* delen av strängen när du använder den senare.
* Konto namn:
  * Extrahera namnet på din katalog från Atlas slut punkts strängen. Till exempel, om din Atlas-slutpunkt är `https://ThisIsMyCatalog.catalog.purview.azure.com` , är ditt konto namn `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Använd Postman-klienten för att anropa REST-API: er

1. Installera [Postman-klienten](https://www.getpostman.com/).
1. Från klienten väljer du **Importera** och använder [Test.postman_collection.jspå](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Välj **samlingar** och välj sedan **testa**.
1. Välj **Hämta token**:
    1. I URL: en bredvid POST ersätter du *&lt; ditt-klient-ID &gt;* med klient-ID: t som du kopierade i föregående avsnitt.
    1. Välj fliken **brödtext** och ersätt plats hållarna i sökvägen och texten från föregående steg.

       När du har valt **Skicka**, innehåller svars texten en JSON-struktur, inklusive namnet *access_token* och ett citerat sträng värde. 
    1. Kopiera värdet för Bearer-token (utan citat tecken) som ska användas i nästa steg.

1. Välj **/v2/types/typedefs**:
    1. Ersätt plats hållaren i sökvägen med ditt Atlas slut punkts värde. Du kan hämta det här värdet genom att gå till katalog instansen på Ibiza-portalen och klicka på Översikt. 

       Bearer-token anges från det första steget (eller så kan du kopiera det på fliken "auktorisering" manuellt).

    1. Välj **Skicka** för att hämta svaret.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Skapa en .NET-klient för att anropa REST-API: erna

### <a name="install-autorest"></a>Installera AutoRest



1. [Installera Node.js](https://github.com/Azure/autorest/blob/master/docs/installing-autorest.md).
1. Starta PowerShell och kör följande kommando:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Ladda ned rest-api-specs.zip och skapa klienten

1. Ladda ned [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) och extrahera filerna.
1. Kör följande kommando i PowerShell från den extraherade mappen REST-API-specifikationer:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   Utdata från den här processen skapar en mapp PurviewCatalogClient och CSharp i mappen REST-API-specifikationer.

### <a name="create-a-sample-net-console-application"></a>Skapa ett exempel på ett .NET-konsol program

1. Öppna Visual Studio 2019. Dessa instruktioner har testats med den kostnads fria community-versionen.
1. På sidan **skapa ett nytt projekt** skapar du ett **konsol program (.net Core)** -projekt i C#.
1. Kopiera och klistra in den angivna [exempel koden](#sample-code-for-the-console-application).
1. Ersätt *accountName*, *servicePrincipalId*, *servicePrincipalKey* och *tenantId* med de värden som du tidigare har samlat in.
1. Använd **Solution Explorer** för att lägga till en mapp som heter **genererad** i Visual Studio-projektet.
1. Kopiera mappen rest-api-specs\PurviewCatalogClient\CSharp som du skapade tidigare till mappen \Generated. Använd Utforskaren eller kommando tolken för kopierings åtgärden, så att Visual Studio automatiskt lägger till filerna i projektet.
1. I **Solution Explorer** högerklickar du på projektet och väljer sedan **Hantera NuGet-paket**.
1. Välj fliken **Bläddra** . Sök efter och välj **Microsoft. rest. ClientRuntime**.
1. Kontrol lera att versionen är minst 2.3.21 och välj sedan **Installera**.
1. Skapa och kör programmet.

Exempel koden returnerar ett antal av hur många typedefs som finns i katalogen och visar hur du hanterar roll tilldelningar. Mer information finns `DoRoleAssignmentOperations()` i i exempel koden. Mer information om projektet finns i [projekt konfiguration](https://github.com/Azure/autorest/blob/master/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Exempel kod för konsol programmet

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera datakällor](manage-data-sources.md)
