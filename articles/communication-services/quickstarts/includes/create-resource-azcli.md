---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 6ed6544b8014e973eaf92c763ca18687ad89e5a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495914"
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