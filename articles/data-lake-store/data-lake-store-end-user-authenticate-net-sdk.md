---
title: Autentisering med slutanvändare – .NET med Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår autentisering för slutanvändare med Azure Data Lake Storage Gen1 att använda Azure Active Directory med .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89004790"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autentisering med slutanvändare med Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig hur du använder .NET SDK för att utföra autentisering med slutanvändare med Azure Data Lake Storage Gen1. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK, se [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Förutsättningar
* **Visual Studio 2013 eller senare**. Anvisningarna nedan använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "internt"-program**. Du måste ha slutfört stegen i [slut användar autentisering med data Lake Storage gen1 med hjälp av Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. I Visual Studio väljer du **Arkiv** -menyn, **nytt** och sedan **projekt**.
2. Välj **konsol program (.NET Framework)** och välj sedan **Nästa**.
3. I **projekt namn** anger du `CreateADLApplication` och väljer sedan **skapa**.

4. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. På fliken **NuGet Package Manager** ser du till att **paket källan** är inställd på **NuGet.org** och att kryss rutan **Inkludera för hands version** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.Management.DataLake.Store` – I den här självstudiekursen används v2.1.3-förhandsversionen.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` –I den här självstudiekursen används v2.2.12.

        ![Lägg till en NuGet-källa](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Skapa ett nytt Azure Data Lake konto")
   4. Stäng **NuGet Package Manager**.

5. Öppna **program. cs**
6. Ersätt using using-uttryck med följande rader:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Lägg till det här kodfragmentet i ditt .NET-klient program. Ersätt plats hållarna med värdena som hämtats från ett internt Azure AD-program (listat som krav). Det här kodfragmentet gör att du kan autentisera ditt program **interaktivt** med data Lake Storage gen1, vilket innebär att du uppmanas att ange dina autentiseringsuppgifter för Azure.

För enkel användning använder följande kodfragment standardvärden för klient-ID och omdirigerings-URI som är giltiga för alla Azure-prenumerationer. I följande kodfragment behöver du bara ange värdet för klient-ID: t. Du kan hämta klient-ID: t med hjälp av anvisningarna i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
- Ersätt Main ()-funktionen med följande kod:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Några saker att känna till i föregående kodfragment:

* Föregående kodfragment använder en hjälp funktion `GetTokenCache` och `GetCreds_User_Popup` . Koden för dessa hjälp funktioner finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* För att hjälpa dig att slutföra självstudien snabbare använder kodfragmentet ett internt program klient-ID som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och ett eget programklient-ID måste du skapa ett eget Azure AD-program och sedan använda Azure AD-domänens ID, klient-ID och omdirigerings-URI för det program som du skapade. Instruktioner finns i [skapa ett Active Directory program för slutanvändarens autentisering med data Lake Storage gen1](data-lake-store-end-user-authenticate-using-active-directory.md) .

  
## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder autentisering med slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med .NET SDK. Nu kan du titta på följande artiklar som talar om hur du använder .NET SDK för att arbeta med Azure Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)

