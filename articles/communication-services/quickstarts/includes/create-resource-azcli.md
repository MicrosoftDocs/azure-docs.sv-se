---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: 32d869e6285e76b57a7b8e462e8110a43de82dd9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656588"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Skapa Azure Communication-resurs

Om du vill skapa en Azure Communication Services-resurs [loggar du in på Azure CLI](/cli/azure/authenticate-azure-cli)och kör sedan följande kommando:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Du kan konfigurera resursen för kommunikations tjänster med följande alternativ:

* Resurs gruppen
* Namnet på kommunikations tjänst resursen
* Den geografi som resursen ska associeras med

I nästa steg kan du tilldela-taggar till resursen. Taggar kan användas för att organisera dina Azure-resurser. Mer information om taggar finns i [dokumentationen för resurs taggning](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Hantera kommunikations tjänst resursen

Kör följande kommandon för att lägga till taggar till kommunikations tjänst resursen:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Mer information om ytterligare kommandon finns i [AZ-kommunikation](/cli/azure/ext/communication/communication).