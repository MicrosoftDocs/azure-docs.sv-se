---
title: Skapa en Azure Cognitive Services-resurs med ARM-mallar | Microsoft Docs
description: Skapa en Azure-tjänst resurs med ARM-mall.
keywords: kognitiva tjänster, kognitiva lösningar, kognitiv intelligens, kognitiv artificiell intelligens
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 161c5779926acad8814ec057f24e36f371738483
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864371"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Snabb start: skapa en Cognitive Services resurs med en ARM-mall

Den här snabb starten beskriver hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa Cognitive Services.

Azure Cognitive Services är Cloud-Base-tjänster med REST API: er och SDK: er för klient bibliotek som är tillgängliga för att hjälpa utvecklare att bygga kognitiv information i program utan att ha direkt artificiell intelligens (AI) eller data vetenskaps kunskaper eller kunskap. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja på skäl.

Skapa en resurs med hjälp av en Azure Resource Manager-mall (ARM-mall). Med den här resursen med flera tjänster kan du:

* Få åtkomst till flera Azure-Cognitive Services med en enda nyckel och slut punkt.
* Konsolidera faktureringen från de tjänster som du använder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera din kognitiva tjänst till Azure](../media/template-deployments/deploy-to-azure.svg "Distribuera din kognitiva tjänst till Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har någon Azure-prenumeration kan du [skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

En Azure-resurs har definierats i mallen:
* [Microsoft. CognitiveServices/Accounts](/azure/templates/microsoft.cognitiveservices/accounts): skapar en Cognitive Services-resurs.

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Klicka på knappen **distribuera till Azure** .

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Ange följande värden.

    |Värde  |Beskrivning  |
    |---------|---------|
    | **Prenumeration** | Välj en Azure-prenumeration. |
    | **Resursgrupp** | Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**. |
    | **Region** | Välj en region.  Till exempel **USA, östra** |
    | **Kognitivt tjänst namn** | Ersätt med ett unikt namn för din resurs. Du kommer att behöva namnet i nästa avsnitt när du validerar distributionen. |
    | **Plats** | Ersätt med den region som används ovan. |
    | **SKU** | [Pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/) för din resurs. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Skärmen skapa resurs.":::

3. Välj **Granska + skapa** och klicka sedan på **Skapa**. När resursen har slutfört distributionen markeras knappen **gå till resurs** .

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group` Create kräver Azure CLI version 2,6 eller senare. För att Visa versions typen `az --version` . Mer information finns i [dokumentationen](/cli/azure/deployment/group).

Kör följande skript med hjälp av Azure Command Line Interface (CLI) [på den lokala datorn](/cli/azure/install-azure-cli)eller i en webbläsare med knappen **prova** . Ange ett namn och en plats (till exempel `centralus` ) för en ny resurs grupp och arm-mallen kommer att användas för att distribuera en Cognitive Services resurs i den. Kom ihåg det namn som du använder. Du kommer att använda den senare för att verifiera distributionen.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/portal)

När distributionen är klar kan du klicka på knappen **gå till resurs** för att se din nya resurs. Du kan också hitta resurs gruppen genom att:

1. Välj **resurs grupper** på den vänstra navigerings menyn.
2. Välja resurs gruppens namn.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Använd Azure CLI och kör följande skript och ange namnet på den resurs grupp som du skapade tidigare.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta upp resurs gruppen som innehåller resursen som ska tas bort
3. Högerklicka på listan över resurs grupper. Välj **Ta bort resursgrupp** och bekräfta.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Använd Azure CLI och kör följande skript och ange namnet på den resurs grupp som du skapade tidigare.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="see-also"></a>Se även

* Se **[autentisera begär anden till Azure Cognitive Services](authentication.md)** om hur du säkert arbetar med Cognitive Services.
* Se **[Vad är Azure Cognitive Services?](./what-are-cognitive-services.md)** för att hämta en lista över olika kategorier i Cognitive Services.
* Se den **[naturliga språk supporten](language-support.md)** om du vill se en lista över naturliga språk som Cognitive Services stöder.
* Se **[använda Cognitive Services som behållare](cognitive-services-container-support.md)** för att förstå hur du använder Cognitive Services lokal.
* Se **[planera och hantera kostnader för Cognitive Services](plan-manage-costs.md)** för att beräkna kostnaden för att använda Cognitive Services.
