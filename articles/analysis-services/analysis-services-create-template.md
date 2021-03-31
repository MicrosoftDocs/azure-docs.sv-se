---
title: Snabb start – skapa en Azure Analysis Services server resurs genom att använda Azure Resource Manager mall
description: Snabb start visar hur du Azure Analysis Services server resurser genom att använda en Azure Resource Manager-mall.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230797"
---
# <a name="quickstart-create-a-server---arm-template"></a>Snabb start: skapa en server ARM-mall

I den här snabb starten beskrivs hur du skapar en Analysis Services server resurs i din Azure-prenumeration med hjälp av en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Besök [Azures kostnads fri utvärderings version](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation. Och du måste vara inloggad på Azure med ett konto i den Azure Active Directory. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

En enkel [Microsoft. AnalysisServices/servers-](/azure/templates/microsoft.analysisservices/servers) resurs med en brand Väggs regel definieras i mallen.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande distribution till Azure-länk för att logga in på Azure och öppna en mall. Mallen används för att skapa en Analysis Services server resurs och ange obligatoriska och valfria egenskaper.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    Om inget annat anges använder du standardvärdena.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Klicka på **Skapa ny** och ange sedan ett unikt namn för den nya resurs gruppen.
    * **Plats**: Välj en standard plats för resurser som skapats i resurs gruppen.
    * **Server namn**: Ange ett namn för Server resursen. 
    * **Plats**: ignorera för Analysis Services. Platsen anges på Server platsen.
    * **Server plats**: Ange Analysis Services serverns plats. Detta är ofta samma region som standard platsen som angetts för resurs gruppen, men krävs inte. Till exempel **norra centrala USA**. Information om regioner som stöds finns i [Analysis Services tillgänglighet per region](analysis-services-overview.md#availability-by-region).
    * **SKU-namn**: Ange SKU-namnet för den Analysis Services-server som ska skapas. Välj mellan: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. SKU-tillgänglighet är beroende av region. S0 eller D1 rekommenderas för utvärdering och testning.
    * **Kapacitet**: Ange det totala antalet instanser av en utskalning av frågans replik. Det finns endast stöd för att skala ut fler än en instans i SELECT-regioner.
    * **Brand Väggs inställningar**: Ange regler för inkommande brand vägg som ska definieras för-servern. Om inget anges inaktive ras brand väggen.
    * **Säkerhetskopiera BLOB container-URI**: Ange SAS-URI: n till en privat Azure Blob Storage-behållare med Läs-, skriv-och list behörigheter. Krävs endast om du avser att använda [säkerhets kopiering/återställning](analysis-services-backup.md).
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När servern har distribuerats visas ett meddelande:

   ![ARM-mall, distribuera Portal meddelande](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Använd Azure Portal eller Azure PowerShell för att kontrol lera att resurs gruppen och Server resursen har skapats.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du Azure Portal, Azure CLI eller Azure PowerShell för att ta bort resurs gruppen och Server resursen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du en ARM-mall för att skapa en ny resurs grupp och en Azure Analysis Services server resurs. När du har skapat en server resurs med hjälp av mallen bör du tänka på följande:

> [!div class="nextstepaction"]
> [Snabbstart: Konfigurera serverbrandväggen – portalen](analysis-services-qs-firewall.md)   
