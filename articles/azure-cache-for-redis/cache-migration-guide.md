---
title: Migrera till Azure Cache for Redis
description: Lär dig hur du migrerar din befintliga cache till Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451531"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrera till Azure Cache for Redis
Den här artikeln beskriver ett antal metoder för att migrera en befintlig Redis-cache som körs lokalt eller i en annan moln tjänst till Azure cache för Redis.

## <a name="migration-scenarios"></a>Migreringsscenarier
Redis med öppen källkod kan köras i många beräknings miljöer. Vanliga exempel är:

- **Lokala** Redis-cacheminnen som körs i privata data Center.
- **Molnbaserade virtuella datorer** – Redis cacheminnen som körs på virtuella Azure-datorer, AWS EC2 och så vidare.
- **Värd tjänster** – hanterade Redis-tjänster som AWS ElastiCache.
- **Olika regioner** – Redis-cache finns i en annan Azure-region.

Om du har ett sådant cacheminne kanske du kan flytta det till Azure cache för Redis med minimala avbrott eller stillestånds tid.

## <a name="migration-options"></a>Migreringsalternativ

Det finns olika sätt på vilka du kan växla från ett cacheminne till en annan. Beroende på var cachen är och hur ditt program interagerar med det, är en metod mer användbar än de andra. Några av de ofta använda migrations strategierna beskrivs nedan.

   | Alternativ       | Fördelar | Nackdelar |
   | ------------ | ---------- | ------------- |
   | Skapa en ny cache | Enklaste att implementera. | Du måste fylla i data till det nya cacheminnet, vilket kanske inte fungerar med många program. |
   | Exportera och importera data via RDB-fil | Är kompatibel med alla Redis-cache i allmänhet. | Vissa data kan gå förlorade om de skrivs till den befintliga cachen när RDB-filen har genererats. | 
   | Data med dubbel skrivning till två cacheminnen | Ingen data förlust eller stillestånds tid. Oavbruten åtgärd i det befintliga cacheminnet. Enklare testning av det nya cacheminnet. | Behöver två cacheminnen under en längre tids period. | 
   | Migrera data program mässigt | Fullständig kontroll över hur data flyttas. | Anpassad kod krävs. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Skapa en ny Azure-cache för Redis

Den här metoden är tekniskt sett inte en migrering. Om det inte är något problem att förlora data är det enklaste sättet att flytta till Azure cache för Redis att skapa en cache-instans och ansluta ditt program till den. Om du till exempel använder Redis som en fristående cache med databas poster kan du enkelt återskapa cacheminnet från grunden.

Allmänna steg för att implementera det här alternativet är:

1. Skapa en ny Azure-cache för Redis-instans.

2. Uppdatera programmet så att det använder den nya instansen.

3. Ta bort den gamla Redis-instansen.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Exportera data till en RDB-fil och importera den till Azure cache för Redis

Redis med öppen källkod definierar en standardmekanism för att ta en ögonblicks bild av en caches minnes intern data uppsättning och spara den i en fil. Den här filen, som kallas RDB, kan läsas av en annan Redis-cache. [Azure cache för Redis Premium-nivån](cache-overview.md#service-tiers) stöder import av data till en cache-instans via RDB-filer. Du kan använda en RDB-fil för att överföra data från ett befintligt cacheminne till Azure cache för Redis.

> [!IMPORTANT]
> RDB-filformatet kan ändras mellan Redis-versioner och kan inte upprätthålla bakåtkompatibilitet. Redis-versionen för det cacheminne som du exporterar från måste vara lika med eller lägre än den version som tillhandahålls av Azure cache för Redis.
>

Allmänna steg för att implementera det här alternativet är:

1. Skapa en ny Azure-cache för Redis-instans på Premium-nivån som har samma storlek som (eller större än) det befintliga cacheminnet.

2. Spara en ögonblicks bild av den befintliga Redis-cachen. Du kan [Konfigurera Redis för att spara ögonblicks bilder](https://redis.io/topics/persistence) regelbundet eller köra processen manuellt med hjälp av [Spara](https://redis.io/commands/save) -eller [BGSAVE](https://redis.io/commands/bgsave) -kommandona. RDB-filen heter "dump. RDB" som standard och kommer att finnas på den sökväg som anges i konfigurations filen *Redis. conf* .

    > [!NOTE]
    > Om du migrerar data i Azure cache för Redis kan du läsa [följande instruktioner om hur du exporterar en RDB-fil](cache-how-to-import-export-data.md) eller använder [PowerShell Export-cmdleten](/powershell/module/azurerm.rediscache/export-azurermrediscache) i stället.
    >

3. Kopiera RDB-filen till ett Azure Storage-konto i den region där det nya cacheminnet finns. Du kan använda AzCopy för den här uppgiften.

4. Importera RDB-filen till den nya cachen med hjälp av [import instruktionerna](cache-how-to-import-export-data.md) eller [PowerShell-import-cmdleten](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Uppdatera programmet så att det använder den nya cache-instansen.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Skriv till två Redis-cache samtidigt under migrerings perioden

I stället för att flytta data direkt mellan cacheminnen kan du använda ditt program för att skriva data till både en befintlig cache och en ny som du konfigurerar. Programmet kommer fortfarande att läsa data från den befintliga cachen från början. När den nya cachen har nödvändiga data växlar du programmet till denna cache och drar tillbaka det gamla. Anta till exempel att du använder Redis som ett migreringsarkiv och att programsessionerna är giltiga i sju dagar. När du har skrivit till de två cacheminnena i en vecka är du säker på att den nya cachen innehåller all information om session som inte har gått ut. Du kan på ett säkert sätt lita på den från den punkten utan att behöva oroa dig för data förlust.

Allmänna steg för att implementera det här alternativet är:

1. Skapa en ny Azure-cache för Redis-instans på Premium-nivån som har samma storlek som (eller större än) det befintliga cacheminnet.

2. Ändra program kod för att skriva till både nya och ursprungliga instanser.

3. Fortsätt att läsa data från den ursprungliga instansen tills den nya instansen är tillräckligt ifylld med data.

4. Uppdatera program koden för att läsa och skriva från den nya instansen.

5. Ta bort den ursprungliga instansen.

### <a name="migrate-programmatically"></a>Migrera program mässigt

Du kan skapa en anpassad migreringsprocessen genom att läsa data program mässigt från en befintlig cache och skriva dem i Azure cache för Redis. Det här [verktyget med öppen källkod](https://github.com/deepakverma/redis-copy) kan användas för att kopiera data från en Azure-cache för Redis-instans till en annan. Det här verktyget är användbart för att flytta data mellan cache-instanser i olika Azure cache-regioner. En [kompilerad version](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) är också tillgänglig. Du kan också hitta käll koden som en användbar guide för att skriva ditt eget Migreringsverktyg.

> [!NOTE]
> Det här verktyget stöds inte officiellt av Microsoft. 
>

Allmänna steg för att implementera det här alternativet är:

1. Skapa en virtuell dator i den region där den befintliga cachen finns. Om din data uppsättning är stor väljer du en relativt kraftfull virtuell dator för att minska kopierings tiden.

2. Skapa en ny Azure-cache för Redis-instans.

3. Rensa data från det nya cacheminnet för att se till att den är tom. Det här steget är obligatoriskt eftersom själva kopierings verktyget inte skriver över en befintlig nyckel i mål-cachen.

    > [!IMPORTANT]
    > Se till att du inte tömmer käll-cachen.
    >

4. Använd ett program som till exempel verktyget med öppen källkod ovan för att automatisera kopieringen av data från käll-cachen till målet. Kom ihåg att kopierings processen kan ta en stund att slutföra beroende på storleken på din data uppsättning.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis-tjänst nivåer](cache-overview.md#service-tiers)
* [Importera data](cache-how-to-import-export-data.md#import)