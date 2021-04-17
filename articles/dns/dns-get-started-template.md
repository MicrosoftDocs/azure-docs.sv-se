---
title: 'Snabbstart: Skapa en Azure DNS zon och post – Azure Resource Manager (ARM-mall)'
titleSuffix: Azure DNS
description: Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Det här är en stegvis snabbstart för att skapa och hantera din första DNS-zon och DNS-post med hjälp Azure Resource Manager mall (ARM-mall).
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539023"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Snabbstart: Skapa en Azure DNS zon och post med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en DNS-zon `A` med en post i den.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

I den här snabbstarten skapar du en unik DNS-zon med suffixet `azurequickstart.org` . En `A` post som pekar på två IP-adresser placeras också i zonen.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Två Azure-resurser har definierats i mallen:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A:**](/azure/templates/microsoft.network/dnsZones/A)Används för att skapa `A` en post i zonen.

Du hittar fler mallar som är relaterade till Azure Traffic Manager i [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova från** följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från konsolen.

1. Välj **Kopiera** från föregående kodblock för att kopiera PowerShell-skriptet.

1. Högerklicka på gränssnittskonsolfönstret och välj klistra **in**.

1. Ange värdena.

    Malldistributionen skapar en zon med en post `A` som pekar på två IP-adresser. Resursgruppens namn är projektnamnet med **rg** tillagt.

    Det tar några sekunder att distribuera mallen. När du är klar ser utdata ut ungefär så här:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS utdata Resource Manager Mall för PowerShell-distribution":::

Azure PowerShell används för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardresursgruppens namn är projektnamnet med **rg** tillagt.

1. Resursgruppen ska innehålla följande resurser som visas här:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Resursgrupp för distribution av DNS-zon":::

1. Välj DNS-zonen med suffixet för att verifiera att zonen har skapats korrekt med en post som `azurequickstart.org` `A` refererar till värdet `1.2.3.4` för och `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Distribution av DNS-zon":::

1. Kopiera ett av namnservernamnen från föregående steg.

1. Öppna en kommandotolk och kör följande kommando:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Exempel:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Du bör se något som liknar följande skärmbild:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS-zon nslookup":::

Värdnamnet `www.2lwynbseszpam.azurequickstart.org` matchas till `1.2.3.4` och , precis som du `1.2.3.5` konfigurerade det. Resultatet verifierar att namnmatchningen fungerar korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när du inte längre behöver de resurser som du skapade med DNS-zonen. Detta tar bort DNS-zonen och alla relaterade resurser.

Om du vill ta bort resursgruppen anropar du `Remove-AzResourceGroup` cmdleten :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

- DNS-zon
- `A` Post

Nu när du har skapat din första DNS-zon och DNS-post med hjälp av en ARM-mall kan du skapa poster för en webbapp i en anpassad domän.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
