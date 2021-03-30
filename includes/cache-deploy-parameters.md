---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187152"
---
### <a name="cacheskuname"></a>cacheSKUName

Pris nivån för den nya Azure-cachen för Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Mallen definierar de värden som tillåts för den här parametern (Basic, standard eller Premium) och tilldelar ett standardvärde (Basic) om inget värde har angetts. Basic tillhandahåller en enda nod med flera storlekar som är tillgängliga upp till 53 GB. Standard tillhandahåller primär/replik med två noder med flera storlekar som är tillgängliga upp till 53 GB och 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Familjen för SKU: n.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Storleken på den nya Azure-cachen för Redis-instansen.

För Basic-och standard-familjer:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Värdet för värdet cache för Premium är detsamma, men de tillåtna värdena körs från 1 till 5 i stället för från 0 till 6.

Mallen definierar de heltals värden som tillåts för den här parametern (0 till 6 för Basic-och standard-familjer, 1 till 5 för Premium-serien). Om inget värde anges tilldelar mallen standardvärdet 0 för Basic och standard, 1 för Premium.

Värdena motsvarar följande cachestorlek:

| Värde | Basic och standard<br>cachestorlek | Premium<br>cachestorlek |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (standard)                 | saknas                   |
| 1     | 1 GB                             | 6 GB (standard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | saknas                   |
