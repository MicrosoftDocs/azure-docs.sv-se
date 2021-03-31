---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622024"
---
Emulatorn har stöd för ett enda fast konto och en välkänd autentiseringsnyckel för autentisering med delad nyckel. Det här kontot och nyckeln är de enda autentiseringsuppgifterna för delad nyckel som tillåts för användning med emulatorn. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Den autentiseringsnyckel som stöds av emulatorn är endast avsedd för att testa funktionen hos din klientautentisering. Det fungerar inte i något säkerhets syfte. Du kan inte använda ditt produktions lagrings konto och din nyckel med emulatorn. Du bör inte använda utvecklings kontot med produktions data.
>
> Emulatorn stöder endast anslutning via HTTP. HTTPS är dock det rekommenderade protokollet för att få åtkomst till resurser i ett Azure Storage-konto för produktion.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Anslut till emulator-kontot med hjälp av genvägen

Det enklaste sättet att ansluta till emulatorn från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen `UseDevelopmentStorage=true` . Genvägen motsvarar den fullständiga anslutnings strängen för emulatorn, som anger konto namnet, konto nyckeln och emulatorns slut punkter för var och en av de Azure Storage tjänsterna:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

Följande .NET-kodfragment visar hur du kan använda genvägen från en metod som tar en anslutnings sträng. Till exempel tar [BlobContainerClient (sträng, sträng)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) en anslutnings sträng.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Kontrol lera att emulatorn körs innan du anropar koden i kodfragmentet.
