---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77474151"
---
### <a name="query-the-storage-queue"></a>Fråga lagrings kön

Du kan använda [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att Visa lagrings köer i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med namnet `outqueue` , som är kön som skapades när funktionen kördes.

Använd sedan [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) kommandot för att visa meddelanden i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Den returnerade strängen ska vara samma som det meddelande som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Detta beror på att Queue Storage-bindningarna skriver till och läser från Azure Storage som [base64-strängar](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).