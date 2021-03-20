---
title: Uppgradera till det senaste klient biblioteket för Elastic Database
description: Använd NuGet för att uppgradera klient biblioteket för Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793423"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uppgradera en app för att använda det senaste klient biblioteket för Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nya versioner av [Elastic Database klient biblioteket](elastic-database-client-library.md) är tillgängliga via NuGet och NuGet Package Manager-gränssnittet i Visual Studio. Uppgraderingar innehåller fel korrigeringar och stöd för nya funktioner i klient biblioteket.

**För den senaste versionen:** Gå till [Microsoft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Återskapa ditt program med det nya biblioteket, samt ändra dina befintliga Shard för kart hanterare som lagras i databaserna i Azure SQL Database för att stödja nya funktioner.

Genom att utföra de här stegen i ordning ser du till att gamla versioner av klient biblioteket inte längre finns i din miljö när metadata-objekt uppdateras, vilket innebär att metadata-objekt från gammal version inte skapas efter uppgraderingen.

## <a name="upgrade-steps"></a>Uppgraderings steg

**1. uppgradera dina program.** I Visual Studio kan du hämta och referera till den senaste klient biblioteks versionen i alla dina utvecklings projekt som använder biblioteket. återskapa och distribuera sedan.

* I din Visual Studio-lösning väljer du **verktyg**  -->  **NuGet Package Manager**  -->   **Hantera NuGet-paket för lösningen**.
* (Visual Studio 2013) Välj **uppdateringar** i den vänstra panelen och välj sedan knappen **uppdatera** i paketet **Azure SQL Database klient biblioteket för elastisk skala** som visas i fönstret.
* (Visual Studio 2015) Ange att filter rutan ska **uppgraderas**. Välj det paket som ska uppdateras och klicka på knappen **Uppdatera** .
* (Visual Studio 2017) Överst i dialog rutan väljer du **uppdateringar**. Välj det paket som ska uppdateras och klicka på knappen **Uppdatera** .
* Bygg och distribuera.

**2. uppgradera skripten.** Om du använder **PowerShell** -skript för att hantera Shards, [laddar du ned den nya biblioteks versionen](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) och kopierar den till den katalog som du kör skripten från.

**3. uppgradera tjänsten för delad sammanslagning.** Om du använder verktyget för delning och sammanslagning av elastiska databaser för att omorganisera shardade-data, [laddar du ned och distribuerar den senaste versionen av verktyget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detaljerade uppgraderings steg för tjänsten hittar du [här](elastic-scale-overview-split-and-merge.md).

**4. uppgradera Shard Map Manager-databaserna**. Uppgradera metadata som stöder dina Shard-kartor i Azure SQL Database.  Det finns två sätt som du kan utföra med hjälp av PowerShell eller C#. Båda alternativen visas nedan.

***Alternativ 1: uppgradera metadata med PowerShell***

1. Hämta det senaste kommando rads verktyget för NuGet [härifrån och spara det i en](https://nuget.org/nuget.exe) mapp.
2. Öppna en kommando tolk, navigera till samma mapp och utfärda kommandot: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navigera till undermappen som innehåller den nya klient-DLL-version som du precis har laddat ned, till exempel: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Hämta klient uppgraderings skriptet för Elastic Database från [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)och spara det i samma mapp som innehåller DLL-filen.
5. Från den mappen kör du PowerShell .\upgrade.ps1 från kommando tolken och följer anvisningarna.

***Alternativ 2: uppgradera metadata med C #***

Du kan också skapa ett Visual Studio-program som öppnar din ShardMapManager, itereras över alla Shards och utför metadata-uppgraderingen genom att anropa metoderna [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) och [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) som i det här exemplet:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Dessa tekniker för uppgraderingar av metadata kan tillämpas flera gånger utan att skadas. Om exempelvis en äldre klient version oavsiktligt skapar en Shard när du redan har uppdaterat, kan du köra uppgraderingen igen över alla Shards för att säkerställa att den senaste metadata-versionen finns i hela infrastrukturen.

**Obs:**  Nya versioner av klient biblioteket publicerat hittills fortsätter att fungera med tidigare versioner av Shard Map Manager-metadata på Azure SQL Database och vice versa.   Men för att dra nytta av några av de nya funktionerna i den senaste klienten måste metadata uppgraderas.   Observera att metadata-uppgraderingar inte påverkar användar data eller programspecifika data, endast objekt som skapats och används av Shard Map Manager.  Och program fortsätter att köras genom den uppgraderings ordning som beskrivs ovan.

## <a name="elastic-database-client-version-history"></a>Versions historik för Elastic Database-klient

För versions historik går du till [Microsoft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png