---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645461"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Om du inte har använt Event Grid förut i din Azure-prenumeration kan du behöva registrera Event Grid-resursprovidern. Kör följande kommando:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Det kan ta en stund att slutföra registreringen. Du kan kontrollera status genom att köra:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

När `RegistrationStatus` är `Registered` kan du fortsätta.
