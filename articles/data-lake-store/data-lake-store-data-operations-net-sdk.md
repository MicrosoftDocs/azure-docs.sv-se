---
title: .NET SDK – fil Systems åtgärder på Data Lake Storage Gen1 – Azure
description: Använd Azure Data Lake Storage Gen1 .NET SDK för fil Systems åtgärder på Data Lake Storage Gen1, till exempel skapa mappar osv.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: faa539119bbb8d73e66788b8ba472cf0db26a2e4
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431745"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Fil Systems åtgärder på Data Lake Storage Gen1 med hjälp av .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

I den här artikeln får du lära dig hur du utför fil Systems åtgärder på Data Lake Storage Gen1 med hjälp av .NET SDK. Fil Systems åtgärder omfattar att skapa mappar i ett Data Lake Storage Gen1 konto, ladda upp filer, hämta filer osv.

Instruktioner för hur du utför konto hanterings åtgärder på Data Lake Storage Gen1 med hjälp av .NET SDK finns i [konto hanterings åtgärder på data Lake Storage gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

* **Visual Studio 2013 eller senare**. Anvisningarna i den här artikeln använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett konto finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program

Kodavsnittet som finns tillgängligt [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) ger dig en genomgång av processen att skapa filer i arkivet, sammanfoga filer, hämta en fil och ta bort några filer i arkivet. Det här avsnittet av artikeln går igenom de viktigaste delarna av koden.

1. I Visual Studio väljer du **Arkiv** -menyn, **nytt** och sedan **projekt**.
1. Välj **konsol program (.NET Framework)** och välj sedan **Nästa**.
1. I **projekt namn** anger du `CreateADLApplication` och väljer sedan **skapa**.
1. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   1. På fliken **NuGet Package Manager** ser du till att **paket källan** är inställd på **NuGet.org**. Kontrol lera också att kryss rutan **Inkludera för hands version** är markerad.
   1. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.DataLake.Store` – Den här artikeln använder v-1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Den här artikeln använder v 2.3.1.

      Stäng **NuGet Package Manager**.

1. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Deklarera variablerna enligt nedan och ange värden för platshållarna. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder, till exempel autentisering, fil överföring osv.

## <a name="authentication"></a>Autentisering

* För slutanvändarens autentisering för programmet kan du läsa om autentisering med slutanvändare [med data Lake Storage gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Skapa klientobjekt

Följande fragment skapar ett Data Lake Storage Gen1 filesystem-klientcertifikat som används för att skicka begär anden till tjänsten.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Skapa en fil och mapp

Lägg till följande kodfragment i ditt program. Det här kodfragmentet lägger till en fil och en överordnad katalog som inte finns.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Lägg till till en fil

Följande fragment lägger till data i en befintlig fil i Data Lake Storage Gen1-kontot.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Läs en fil

Följande kodfragment läser innehållet i en fil i Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Hämta filegenskaper

Följande kodavsnitt returnerar egenskaperna som är associerade med en fil eller katalog.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Definitionen av `PrintDirectoryEntry` metoden är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Byt namn på en fil

Följande fragment byter namn på en befintlig fil i ett Data Lake Storage Gen1 konto.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Räkna upp en katalog

Följande kodfragment räknar upp kataloger i ett Data Lake Storage Gen1-konto.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definitionen av `PrintDirectoryEntry` metoden är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Ta bort kataloger rekursivt

Följande kodfragment tar bort en katalog och alla dess under kataloger, rekursivt.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Exempel

Här följer några exempel som visar hur du använder SDK för Data Lake Storage Gen1-filsystem.

* [Grundläggande exempel på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Avancerat exempel på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Se även

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK-referens](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)