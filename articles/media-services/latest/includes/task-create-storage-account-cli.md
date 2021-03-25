---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104270"
---
<!-- ### Create a storage account -->

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Mer information om hur lagringskonton används i Media Services finns i [Lagringskonton](../storage-account-concept.md).

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Alternativ för Azure Storage-konton](../../../storage/common/storage-account-overview.md). 

I det här exemplet skapar vi ett General Purpose v2, Standard LRS-konto. Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som ger geografisk replikering för affärskontinuitet. Mer information finns i [lagringskonton](/cli/azure/storage/account).

Följande kommando skapar ett lagringskonto som ska associeras med Media Services-kontot. I skriptet nedan ersätter `storageaccountforams` du med ditt eget unikt-namn med en längd på färre än 24 tecken. `amsResourceGroup` måste matcha det värde som du gav för resurs gruppen i föregående steg.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
