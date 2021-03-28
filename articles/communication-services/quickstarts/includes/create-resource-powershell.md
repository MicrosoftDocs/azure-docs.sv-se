---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644734"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- Installera [Azure AZ PowerShell-modulen](https://docs.microsoft.com/powershell/azure/)

## <a name="create-azure-communication-resource"></a>Skapa Azure Communication-resurs

[Logga in på Azure CLI](/cli/azure/authenticate-azure-cli)för att skapa en Azure Communication Services-resurs. Du kan göra detta via terminalen med hjälp av ```Connect-AzAccount``` kommandot och ange dina autentiseringsuppgifter. Kör följande kommando för att skapa resursen:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Om du vill välja en speciell prenumeration kan du också ange en ```--subscription``` flagga och ange prenumerations-ID: t.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Du kan konfigurera resursen för kommunikations tjänster med följande alternativ:

* Resurs gruppen
* Namnet på kommunikations tjänst resursen
* Den geografi som resursen ska associeras med

I nästa steg kan du tilldela-taggar till resursen. Taggar kan användas för att organisera dina Azure-resurser. Mer information om taggar finns i [dokumentationen för resurs taggning](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Hantera kommunikations tjänst resursen

Kör följande kommandon för att lägga till taggar till kommunikations tjänst resursen. Du kan också rikta en speciell prenumeration.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Om du vill visa en lista över alla dina Azure Communication Services-resurser i en specifik prenumeration använder du följande kommando:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Om du vill visa all information om en specifik resurs använder du följande kommando:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
