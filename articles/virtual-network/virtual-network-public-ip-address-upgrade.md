---
title: Uppgradera offentliga IP-adresser
titleSuffix: Azure Virtual Network
description: Uppgradera offentliga IP-adresser från Basic till Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598422"
---
# <a name="upgrade-public-ip-addresses"></a>Uppgradera offentliga IP-adresser

Offentliga AZURE-IP-adresser skapas med en SKU – antingen Basic eller Standard – som avgör aspekter av deras funktioner (inklusive allokeringsmetod, funktionsstöd och vilka resurser de kan associeras med). 

Följande scenarier granskas i den här artikeln:
* Så här uppgraderar du en offentlig IP-adress för Basic SKU till en offentlig IP-standard-SKU (med Hjälp av PowerShell eller CLI)
* Så här migrerar du en klassisk Azure-Reserverad IP till en Azure Resource Manager offentlig IP-adress för Basic-SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Uppgradera offentlig IP-adress från Basic till Standard-SKU

För att uppgradera en offentlig IP-adress får den [](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) inte associeras med någon resurs (mer information om hur du avassociera offentliga IP-adresser finns på den här sidan).

>[!IMPORTANT]
>Offentliga IP-adresser som uppgraderats från Basic till Standard-SKU har fortfarande inga garanterade [tillgänglighetszoner.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Se till att du har detta i åtanke när du väljer vilka resurser som IP-adressen ska associeras med.

---
# <a name="basic-to-standard---powershell"></a>[**Basic till Standard – PowerShell**](#tab/option-upgrade-powershell)

I följande exempel förutsätts att en offentlig IP-adress för [](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) Basic SKU har skapats tidigare, med hjälp av exemplet på den här sidan med en grundläggande offentlig IP-adress **myBasicPublicIP** **i myResourceGroup**.

Om du vill uppgradera IP-adressen kör du kommandona nedan med PowerShell.  Observera att om IP-adressen redan har allokerats statiskt kan det avsnittet hoppas över.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Basic till Standard – CLI**](#tab/option-upgrade-cli)

I följande exempel förutsätts att en offentlig IP-adress för [](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) Basic SKU har skapats tidigare, med hjälp av exemplet på den här sidan med en grundläggande offentlig IP-adress **myBasicPublicIP** **i myResourceGroup**.

Om du vill uppgradera IP-adressen kör du bara kommandona nedan med hjälp av Azure CLI.  Observera att om IP-adressen redan har allokerats statiskt kan det avsnittet hoppas över.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Uppgradera (migrera) en klassisk Reserverad IP till en statisk offentlig IP-adress

Om du vill dra nytta av de nya funktionerna i Azure Resource Manager kan du migrera befintliga offentliga statiska IP-adresser, som kallas reserverade IP-adresser, från den klassiska modellen till den moderna Azure Resource Manager modellen.  Den migrerade offentliga IP-adressen är en Basic SKU-typ.


---

# <a name="reserved-to-basic---powershell"></a>[**Reserverad till Basic – PowerShell**](#tab/option-migrate-powershell)

I följande exempel förutsätter vi att du tidigare har skapat en klassisk Azure-Reserverad IP **myReservedIP** **i myResourceGroup.** En annan förutsättning för migrering är att se till Azure Resource Manager prenumerationen har registrerats för migrering. Detta tas upp i detalj i steg 3 och 4 på [den här sidan.](../virtual-machines/migration-classic-resource-manager-ps.md)

Om du vill migrera Reserverad IP kör du kommandona nedan med hjälp av PowerShell.  Observera att om IP-adressen inte är associerad med någon tjänst (nedan finns en tjänst med namnet **myService**) kan det steget hoppas över.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Föregående kommando visar eventuella varningar och fel som blockerar migreringen. Om valideringen lyckas kan du fortsätta med följande steg för att förbereda och genomföra migreringen:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
En ny resursgrupp i Azure Resource Manager skapas med namnet på den migrerade Reserverad IP (i exemplet ovan är det resursgruppen **myReservedIP-Migrated**).

# <a name="reserved-to-basic---cli"></a>[**Reserverad till Basic – CLI**](#tab/option-migrate-cli)

I följande exempel förutsätter vi att du tidigare har skapat en klassisk Azure Reserverad IP **myReservedIP** **i myResourceGroup.** En annan förutsättning för migrering är att se till Azure Resource Manager prenumerationen har registrerats för migrering. Detta tas upp i detalj i steg 3 och 4 på [den här sidan.](../virtual-machines/migration-classic-resource-manager-cli.md)

Om du vill migrera Reserverad IP kör du kommandona nedan med hjälp av Azure CLI.  Observera att om IP-adressen inte är associerad med någon tjänst (nedan finns det en tjänst med namnet **myService** och **distribution myDeployment)** kan det steget hoppas över.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Föregående kommando visar eventuella varningar och fel som blockerar migreringen. Om valideringen lyckas kan du fortsätta med följande steg för att förbereda och genomföra migreringen:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
En ny resursgrupp i Azure Resource Manager skapas med namnet på den migrerade Reserverad IP (i exemplet ovan är det resursgruppen **myReservedIP-Migrated**).

---

## <a name="limitations"></a>Begränsningar

* För att uppgradera en grundläggande offentlig IP-adress kan den inte associeras med någon Azure-resurs.  Mer information [om hur](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) du avassociera offentliga IP-adresser finns på den här sidan.  För att kunna migrera en Reserverad IP kan den inte heller associeras med någon molntjänst.  Mer information [om hur](./remove-public-ip-address-vm.md) du avassociera reserverade IP-adresser finns på den här sidan.  
* Offentliga IP-adresser som uppgraderats från Basic till Standard SKU har fortfarande inga garanterade [tillgänglighetszoner.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Se till att du har detta i åtanke när du väljer vilka resurser som IP-adressen ska associeras med.
* Du kan inte nedgradera från Standard till Basic.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure, inklusive skillnaden mellan SKU-typerna samt inställningar [för offentliga IP-adresser.](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Lär dig hur [du uppgraderar offentliga Lastbalanserare i Azure från Basic till Standard.](../load-balancer/upgrade-basic-standard.md)
- Förstå [klassiska reserverade AZURE-IP-adresser](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) [och migrering av klassiska resurser till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
