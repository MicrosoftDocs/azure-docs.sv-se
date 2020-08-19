---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 313c8ea9046deea953b4143f1a0264f81da38764
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602404"
---
<!-- ### Create a storage account -->

När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Mer information om hur lagringskonton används i Media Services finns i [Lagringskonton](../storage-account-concept.md).

Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Endast blob-konton tillåts inte som **primära**. Mer information om lagringskonton finns i [Alternativ för Azure Storage-konton](../../../storage/common/storage-account-overview.md). 

I det här exemplet skapar vi ett General Purpose v2, Standard LRS-konto. Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som ger geografisk replikering för affärskontinuitet. Mer information finns i [lagringskonton](/cli/azure/storage/account?view=azure-cli-latest).

Följande kommando skapar ett lagringskonto som ska associeras med Media Services-kontot. I skriptet nedan kan du ersätta `storageaccountforams` med ditt värde. `amsResourceGroup` måste matcha det värde som du gav för resurs gruppen i föregående steg. Lagrings kontots namn får inte vara längre än 24.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
