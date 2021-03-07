---
title: Hantera ett Azure Data Lake Storage Gen1 konto med .NET
description: Lär dig hur du använder .NET SDK för Azure Data Lake Storage Gen1 konto hanterings åtgärder.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430487"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Konto hanterings åtgärder på Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig hur du utför konto hanterings åtgärder på Azure Data Lake Storage Gen1 med .NET SDK. Konto hanterings åtgärder omfattar att skapa ett Data Lake Storage Gen1 konto, lista konton i en Azure-prenumeration, ta bort kontona osv.

Instruktioner för hur du utför data hanterings åtgärder på Data Lake Storage Gen1 med .NET SDK finns i avsnittet [om fil Systems åtgärder på data Lake Storage gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Förutsättningar
* **Visual Studio 2013 eller senare**. Anvisningarna nedan använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

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

6. Deklarera variablerna och ange värden för platshållare. Kontrollera också att den lokala sökvägen och filnamnet som du anger finns på datorn.

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering

* För slutanvändarens autentisering för programmet kan du läsa om autentisering med slutanvändare [med data Lake Storage gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Skapa klientobjekt
Följande fragment skapar det Data Lake Storage Gen1 konto klient objekt, som används för att utfärda begär Anden om konto hantering till tjänsten, till exempel skapa konto, ta bort konto osv.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Följande fragment skapar ett Data Lake Storage Gen1-konto i Azure-prenumerationen som du angav när du skapade klient objekt för Data Lake Storage Gen1-kontot.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Visa en lista över alla Data Lake Storage Gen1-konton i en prenumeration
Lägg till följande metod i klassdefinitionen. I följande kodfragment visas alla Data Lake Storage Gen1-konton inom en specifik Azure-prenumeration.

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1 konto
Följande kodfragment tar bort Data Lake Storage Gen1 kontot som du skapade tidigare.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Se även
* [Fil Systems åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-referens](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Nästa steg
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)