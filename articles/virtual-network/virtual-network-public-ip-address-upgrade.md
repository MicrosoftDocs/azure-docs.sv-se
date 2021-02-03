---
title: Uppgradera offentliga IP-adresser
titleSuffix: Azure Virtual Network
description: Uppgradera offentliga IP-adresser från Basic till standard.
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
ms.openlocfilehash: 93d5d422709fad3d4f732b3b232f67d60d79b507
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493727"
---
# <a name="upgrade-public-ip-addresses"></a>Uppgradera offentliga IP-adresser

Offentliga Azure-IP-adresser skapas med en SKU – antingen Basic eller standard – som bestämmer aspekter av deras funktioner (inklusive allokeringsmetod, funktions stöd och vilka resurser de kan associeras med). 

Följande scenarier granskas i den här artikeln:
* Så här uppgraderar du en grundläggande offentlig IP-adress för SKU till en offentlig IP-adress för standard-SKU (med PowerShell eller CLI)
* Så här migrerar du en klassisk Azure-Reserverad IP till en Azure Resource Manager grundläggande SKU offentlig IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Uppgradera offentlig IP-adress från Basic till standard-SKU

För att kunna uppgradera en offentlig IP-adress, får den inte kopplas till någon resurs (se [den här sidan](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) för mer information om hur du avassocierar offentliga IP-adresser).

>[!IMPORTANT]
>Offentliga IP-adresser som uppgraderas från Basic till standard-SKU: n fortsätter att ha inga [tillgänglighets zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  Det innebär att de inte kan kopplas till en Azure-resurs som antingen är zon-redundant eller knuten till en fördefinierad zon i regioner där det erbjuds.

---
# <a name="basic-to-standard---powershell"></a>[**Basic till standard – PowerShell**](#tab/option-upgrade-powershell)

I följande exempel förutsätter vi att du skapar en grundläggande offentlig IP-adress för SKU, med hjälp av exemplet som gavs på [den här sidan](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) med en grundläggande offentlig IP- **myBasicPublicIP** i **myResourceGroup**.

För att uppgradera IP-adressen kör du bara kommandona nedan med PowerShell.  Observera att om IP-adressen redan är statiskt allokerad, kan avsnittet hoppas över.

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

# <a name="basic-to-standard---cli"></a>[**Basic till standard-CLI**](#tab/option-upgrade-cli)

I följande exempel förutsätter vi att du skapar en grundläggande offentlig IP-adress för SKU, med hjälp av exemplet som gavs på [den här sidan](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) med en grundläggande offentlig IP- **myBasicPublicIP** i **myResourceGroup**.

För att uppgradera IP-adressen kör du bara kommandona nedan med hjälp av Azure CLI.  Observera att om IP-adressen redan är statiskt allokerad, kan avsnittet hoppas över.

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

Om du vill dra nytta av de nya funktionerna i Azure Resource Manager kan du migrera befintliga offentliga statiska IP-adresser, som kallas reserverade IP-adresser, från den klassiska modellen till den moderna Azure Resource Managers modellen.  Den migrerade offentliga IP-adressen är en grundläggande SKU-typ.


---

# <a name="reserved-to-basic---powershell"></a>[**Reserverat till Basic – PowerShell**](#tab/option-migrate-powershell)

I följande exempel förutsätter vi att du skapade en klassisk Azure Reserverad IP- **myReservedIP** i **myResourceGroup**. En annan förutsättning för migrering är att se till att Azure Resource Manager prenumerationen har registrerats för migrering. Detta beskrivs närmare i steg 3 och 4 på [den här sidan](../virtual-machines/migration-classic-resource-manager-ps.md).

För att migrera Reserverad IP kör du kommandona nedan med hjälp av PowerShell.  Observera att om IP-adressen inte är associerad med någon tjänst (nedan finns det en tjänst med namnet **unservice**) kan du hoppa över det steget.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Föregående kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du fortsätta med följande steg för att förbereda och genomföra migreringen:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
En ny resurs grupp i Azure Resource Manager skapas med namnet på den migrerade Reserverad IP (i exemplet ovan blir det resurs gruppen **myReservedIP-migrated**).

# <a name="reserved-to-basic---cli"></a>[**Reserverat till Basic-CLI**](#tab/option-migrate-cli)

I följande exempel förutsätter vi att du skapade en klassisk Azure Reserverad IP- **myReservedIP** i **myResourceGroup**. En annan förutsättning för migrering är att se till att Azure Resource Manager prenumerationen har registrerats för migrering. Detta beskrivs närmare i steg 3 och 4 på [den här sidan](../virtual-machines/migration-classic-resource-manager-cli.md).

För att migrera Reserverad IP kör du kommandona nedan med hjälp av Azure CLI.  Observera att om IP-adressen inte är associerad med någon tjänst (nedan finns en tjänst med namnet IT **-tjänster** och distributions-och distributions-och distributions-och distribution av distributions **data),** så kan

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Föregående kommando visar eventuella varningar och fel som blockerar migreringen. Om verifieringen lyckas kan du fortsätta med följande steg för att förbereda och genomföra migreringen:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
En ny resurs grupp i Azure Resource Manager skapas med namnet på den migrerade Reserverad IP (i exemplet ovan blir det resurs gruppen **myReservedIP-migrated**).

---

## <a name="limitations"></a>Begränsningar

* För att kunna uppgradera en grundläggande offentlig IP-adress kan den inte kopplas till någon Azure-resurs.  Läs [den här sidan](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) om du vill ha mer information om hur du avassocierar offentliga IP-adresser.  På samma sätt kan det inte vara kopplat till någon moln tjänst för att migrera en Reserverad IP.  Läs [den här sidan](./remove-public-ip-address-vm.md) om du vill ha mer information om hur du avassocierar reserverade IP-adresser.  
* Offentliga IP-adresser som uppgraderas från Basic till standard-SKU: n fortsätter att ha inga [tillgänglighets zoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) och kan därför inte kopplas till en Azure-resurs som är antingen en zon-redundant eller zonindelade.  Observera att detta endast gäller för regioner som erbjuder tillgänglighets zoner.
* Det går inte att nedgradera från standard till Basic.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure, inklusive skillnaden mellan SKU-typer och inställningar för [offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lär dig hur du [uppgraderar offentliga Azure-belastningsutjämnare från Basic till standard](../load-balancer/upgrade-basic-standard.md).
- Förstå [klassiska Azure-reserverade IP-adresser](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) och [migrering av klassiska resurser till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
