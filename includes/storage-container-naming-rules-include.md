---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050267"
---
Varje blob i Azure-lagring måste befinna sig i en container. Containern utgör en del av blobnamnet. ph x="1" /&gt; är till exempel namnet på containern i de här exempelblob-URI:erna:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

Ett containernamn måste vara ett giltigt DNS-namn som överensstämmer med följande namngivningsregler:

1. Containernamnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).
2. Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. Flera bindestreck i följd är inte tillåtna i containernamn.
3. Alla bokstäver i ett containernamn måste vara gemener.
4. Containernamn måste vara mellan 3 och 63 tecken långa.

> [!IMPORTANT]
> Observera att containernamn får innehålla endast gemener. Om du tar med en versal i ett containernamn, eller på annat sätt bryter mot namngivningsreglerna, kan du få ett 400-fel (felaktig begäran). 
> 
> 

