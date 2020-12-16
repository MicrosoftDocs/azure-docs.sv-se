---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582491"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ansluta till emulator-kontot med hjälp av en genväg
Det enklaste sättet att ansluta till emulatorn från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen `UseDevelopmentStorage=true` . Här är ett exempel på en anslutnings sträng till emulatorn i en *app.config* -fil: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Är detsamma som att fullständigt ange konto namnet, konto nyckeln och slut punkterna för var och en av de emulator-tjänster som du vill använda i anslutnings strängen. Detta är nödvändigt för att anslutnings strängen ska referera till emulatorns slut punkter, som skiljer sig från dem för ett produktions lagrings konto. Till exempel ser värdet för anslutnings strängen ut så här:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
