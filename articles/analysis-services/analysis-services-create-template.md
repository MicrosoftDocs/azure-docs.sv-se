---
title: Snabbstart – Skapa en Azure Analysis Services-serverresurs med hjälp av Azure Resource Manager mall
description: Snabbstart som visar hur du Azure Analysis Services en serverresurs med hjälp av en Azure Resource Manager mall.
author: minewiskan
ms.author: owend
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
tags:
- azure-resource-manager
ms.custom: devx-track-azurepowershell - subject-armqs - references_regions - mode-arm
ms.openlocfilehash: e7203f4b5890ab81cbf337c5f3201d85a3aef0c0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769379"
---
# <a name="quickstart-create-a-server---arm-template"></a>Snabbstart: Skapa en server – ARM-mall

Den här snabbstarten beskriver hur du skapar en Analysis Services-serverresurs i din Azure-prenumeration med hjälp av en Azure Resource Manager mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration:** Gå [till Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation. Och du måste vara inloggad på Azure med ett konto i den Azure Active Directory. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

En enskild [Microsoft.AnalysisServices/servers-resurs](/azure/templates/microsoft.analysisservices/servers) med en brandväggsregel definieras i mallen.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj länken Distribuera till Azure för att logga in på Azure och öppna en mall. Mallen används för att skapa en Analysis Services serverresurs och ange obligatoriska och valfria egenskaper.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    Använd standardvärdena om inget annat anges.

    * **Prenumeration:** Välj en Azure-prenumeration.
    * **Resursgrupp:** Klicka **på Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen.
    * **Plats:** Välj en standardplats för resurser som skapats i resursgruppen.
    * **Servernamn:** Ange ett namn för serverresursen. 
    * **Plats:** Ignorera för Analysis Services. Plats anges i Serverplats.
    * **Serverplats:** Ange platsen för Analysis Services servern. Det här är ofta samma region som standardplatsen som angetts för resursgruppen, men det krävs inte. Till exempel **USA, norra centrala.** Information om regioner som stöds [finns Analysis Services tillgänglighet per region.](analysis-services-overview.md#availability-by-region)
    * **SKU-namn:** Ange SKU-namnet för den Analysis Services som ska skapas. Välj mellan: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. SKU-tillgängligheten beror på region. S0 eller D1 rekommenderas för utvärdering och testning.
    * **Kapacitet:** Ange det totala antalet skalbara instanser för frågerepliker. Utskalning av fler än en instans stöds endast i utvalda regioner.
    * **Brandväggsinställningar:** Ange inkommande brandväggsregler som ska definieras för servern. Om inget anges inaktiveras brandväggen.
    * **Uri för säkerhetskopieringsblobcontainer:** Ange SAS-URI:en till en privat Azure Blob Storage med läs-, skriv- och listbehörigheter. Krävs endast om du tänker använda [Säkerhetskopiering/återställning.](analysis-services-backup.md)
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När servern har distribuerats får du ett meddelande:

   ![ARM-mall, distribuera portalmeddelande](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Använd Azure Portal eller Azure PowerShell för att verifiera att resursgruppen och serverresursen har skapats.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan Azure Portal, Azure CLI eller Azure PowerShell för att ta bort resursgruppen och serverresursen.

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

I den här snabbstarten använde du en ARM-mall för att skapa en ny resursgrupp och en Azure Analysis Services-serverresurs. När du har skapat en serverresurs med hjälp av mallen bör du tänka på följande:

> [!div class="nextstepaction"]
> [Snabbstart: Konfigurera serverbrandväggen – portalen](analysis-services-qs-firewall.md)   
