---
title: Start händelse för borttagning av Azure Batch pool
description: Referens för start händelse för borttagning av batch-pool. Den här händelsen genereras när en borttagning av en pool har startat.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803756"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har startat. Eftersom poolen ta bort är en asynkron händelse kan du förvänta en borttagnings händelse för poolen som ska genereras när borttagnings åtgärden har slutförts.

 I följande exempel visas innehållet i en start händelse för pool borttagning.

```
{
   "id": "myPool1"
}
```

|Element|Typ|Kommentarer|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
