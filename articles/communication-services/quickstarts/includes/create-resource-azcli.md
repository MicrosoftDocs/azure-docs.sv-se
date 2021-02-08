---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820015"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Skapa Azure Communication-resurs

Om du vill skapa en Azure Communication Services-resurs [loggar du in på Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)och kör sedan följande kommando:

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

Mer information om ytterligare kommandon finns i [AZ-kommunikation](https://docs.microsoft.com/cli/azure/ext/communication/communication).