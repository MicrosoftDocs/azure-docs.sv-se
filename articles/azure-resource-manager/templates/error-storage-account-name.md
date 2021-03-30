---
title: Namn fel för lagrings konto
description: Beskriver de fel som kan uppstå när du anger ett lagrings konto namn.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76153980"
---
# <a name="resolve-errors-for-storage-account-names"></a>Lös fel för lagrings konto namn

I den här artikeln beskrivs namngivnings fel som kan uppstå när du distribuerar ett lagrings konto.

## <a name="symptom"></a>Symptom

Om ditt lagrings konto namn innehåller otillåtna tecken får du ett fel meddelande som:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

För lagrings konton måste du ange ett namn för den resurs som är unik i Azure. Om du inte anger ett unikt namn visas ett felmeddelande likt följande:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Om du distribuerar ett lagrings konto med samma namn som ett befintligt lagrings konto i din prenumeration, men anger en annan plats, får du ett fel meddelande som anger att lagrings kontot redan finns på en annan plats. Ta antingen bort det befintliga lagrings kontot eller ange samma plats som det befintliga lagrings kontot.

## <a name="cause"></a>Orsak

Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får inte innehålla siffror och gemener. Namnet måste vara unikt.

## <a name="solution"></a>Lösning

Kontrol lera att lagrings kontots namn är unikt. Du kan skapa ett unikt namn genom att kombinera din namn konvention med resultatet av funktionen [uniqueString](template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Kontrol lera att namnet på ditt lagrings konto inte överstiger 24 tecken. Funktionen [uniqueString](template-functions-string.md#uniquestring) returnerar 13 tecken. Om du sammanfogar ett prefix eller postfix till **uniqueString** -resultatet anger du ett värde som är 11 tecken eller mindre.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Kontrol lera att namnet på ditt lagrings konto inte innehåller versaler eller specialtecken.