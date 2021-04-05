---
title: 'Snabb start: skapa en Azure DNS zon och en post Azure Resource Manager-mall (ARM-mall)'
titleSuffix: Azure DNS
description: Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Det här är en steg-för-steg-snabb start för att skapa och hantera din första DNS-zon och-post med hjälp av Azure Resource Manager mall (ARM-mall).
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791009"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Snabb start: skapa en Azure DNS zon och en post med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en DNS-zon med en `A` post i den.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

I den här snabb starten skapar du en unik DNS-zon med suffixet `azurequickstart.org` . En `A` post som pekar på två IP-adresser placeras också i zonen.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Två Azure-resurser har definierats i mallen:

- [**Microsoft. Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft. Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): används för att skapa en `A` post i zonen.

Du hittar fler mallar som är relaterade till Azure Traffic Manager i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från-konsolen.

1. Kopiera PowerShell-skriptet genom att välja **Kopiera** från föregående kodblock.

1. Högerklicka på fönstret Shell-konsol och välj sedan **Klistra in**.

1. Ange värdena.

    Mallen för distribution skapar en zon med en `A` post som pekar på två IP-adresser. Resurs gruppens namn är projekt namnet med **RG** tillagt.

    Det tar några sekunder att distribuera mallen. När det är slutfört ser utdata ut ungefär så här:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS Zone Resource Manager-mall utdata för PowerShell-distribution":::

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Resurs gruppen måste innehålla följande resurser som visas här:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Resurs grupp för distribution av DNS-zoner":::

1. Välj DNS-zonen med suffixet för `azurequickstart.org` att kontrol lera att zonen har skapats korrekt med en `A` post som refererar till värdet för `1.2.3.4` och `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Distribution av DNS-zon":::

1. Kopiera ett namn server namn från föregående steg.

1. Öppna en kommandotolk och kör följande kommando:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Exempel:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Du bör se något som liknar följande skärm bild:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS-zon för nslookup":::

Värd namnet `www.2lwynbseszpam.azurequickstart.org` matchas till `1.2.3.4` och `1.2.3.5` , precis som du konfigurerade det. Resultatet verifierar att namnmatchningen fungerar korrekt.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med DNS-zonen tar du bort resurs gruppen. Detta tar bort DNS-zonen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

- DNS-zon
- `A` konkurrentpost

Nu när du har skapat din första DNS-zon och-post med en ARM-mall kan du skapa poster för en webbapp i en anpassad domän.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
