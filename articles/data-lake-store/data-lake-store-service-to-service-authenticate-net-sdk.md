---
title: .NET-tjänst-till-tjänst-autentisering – Data Lake Storage Gen1
description: Lär dig hur du uppnår tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 att använda Azure Active Directory med .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010307"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

I den här artikeln får du lära dig hur du använder .NET SDK för att utföra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. För autentisering med slutanvändare med Data Lake Storage Gen1 med .NET SDK, se [slutanvändarens autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Förutsättningar
* **Visual Studio 2013 eller senare**. Anvisningarna nedan använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

5. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Tjänst-till-tjänst-autentisering med klient hemlighet
Lägg till det här kodfragmentet i ditt .NET-klient program. Ersätt plats hållarna med värdena som hämtats från ett Azure AD-webbprogram (visas som en förutsättning). Det här kodfragmentet gör att du kan autentisera ditt program **icke-interaktivt** med data Lake Storage gen1 med hjälp av klient hemlighet/nyckel för Azure AD-webbprogram.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Föregående kodfragment använder en hjälp funktion `GetCreds_SPI_SecretKey` . Koden för den här hjälp funktionen finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Tjänst-till-tjänst-autentisering med certifikat

Lägg till det här kodfragmentet i ditt .NET-klient program. Ersätt plats hållarna med värdena som hämtats från ett Azure AD-webbprogram (visas som en förutsättning). Det här kodfragmentet gör att du kan autentisera ditt program **icke-interaktivt** med data Lake Storage gen1 med hjälp av certifikatet för ett Azure AD-webbprogram. Instruktioner för hur du skapar ett Azure AD-program finns i [skapa tjänstens huvud namn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Föregående kodfragment använder en hjälp funktion `GetCreds_SPI_Cert` . Koden för den här hjälp funktionen finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med .NET SDK. Nu kan du titta på följande artiklar som talar om hur du använder .NET SDK för att arbeta med Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)
