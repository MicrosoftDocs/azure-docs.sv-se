---
title: Använda redis-cli med Azure Cache for Redis
description: Lär dig hur *redis-cli.exe* som ett kommandoradsverktyg för att interagera med en Azure Cache for Redis som en klient
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 042db51ab51e9da586f028b8aa99e66ffaa36639
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775681"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Använda Redis-kommandoradsverktyget med Azure Cache for Redis

*redis-cli.exe* är ett populärt kommandoradsverktyg för att interagera med en Azure Cache for Redis som klient. Det här verktyget är också tillgängligt för användning med Azure Cache for Redis.

Verktyget är tillgängligt för Windows-plattformar genom att ladda ned [Redis kommandoradsverktyg för Windows.](https://github.com/MSOpenTech/redis/releases/) 

Om du vill köra kommandoradsverktyget på en annan plattform laddar du ned Azure Cache for Redis från [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Samla in information om cacheåtkomst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan samla in den information som behövs för att komma åt cachen med tre metoder:

1. Azure CLI med [az redis list-keys](/cli/azure/redis#az_redis_list_keys)
2. Azure PowerShell hjälp [av Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Använda Azure-portalen.

I det här avsnittet hämtar du nycklarna från Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Aktivera åtkomst för redis-cli.exe

Med Azure Cache for Redis är endast TLS-porten (6380) aktiverad som standard. `redis-cli.exe`Kommandoradsverktyget stöder inte TLS. Du har två konfigurationsalternativ för att använda den:

1. [Aktivera icke-TLS-porten (6379)](cache-configure.md#access-ports)  -  **Den här konfigurationen rekommenderas inte** eftersom åtkomstnycklarna i den här konfigurationen skickas via TCP i klartext. Den här ändringen kan påverka åtkomsten till ditt cacheminne. Det enda scenario där du kan överväga den här konfigurationen är när du bara använder en testcache.

2. Ladda ned och installera [stunnel](https://www.stunnel.org/downloads.html).

    Starta **servern genom att köra stunnel GUI Start.**

    Högerklicka på ikonen för aktivitetsfältet för stunnel-servern och klicka på **Visa loggfönster.**

    På menyn stunnel Log Window klickar du på **Konfiguration Redigera**  >  **konfiguration för** att öppna den aktuella konfigurationsfilen.

    Lägg till följande post *redis-cli.exe* under avsnittet **Tjänstdefinitioner.** Infoga ditt faktiska cachenamn i stället för `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Spara och stäng konfigurationsfilen. 
  
    I menyn stunnel Log Window (loggfönster för stunnel) klickar du **på Configuration** Reload  >  **Configuration (Konfiguration på nytt).**


## <a name="connect-using-the-redis-command-line-tool"></a>Anslut med redis-kommandoradsverktyget.

När du använder stunnel kör *redis-cli.exe* och skickar endast *din port* och åtkomstnyckel *(primär* eller sekundär) för att ansluta till cachen.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Skärmbild som visar att anslutningen till cachen har lyckats.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Om du använder en testcache med den oskyddade porten som inte är TLS kör du och skickar värdnamnet ,  porten och åtkomstnyckeln (primär eller sekundär) för att ansluta till `redis-cli.exe` testcachen.   

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel med redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Nästa steg

Läs mer om att använda [Redis-konsolen för](cache-configure.md#redis-console) att utfärda kommandon.