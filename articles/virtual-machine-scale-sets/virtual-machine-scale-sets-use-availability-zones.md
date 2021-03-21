---
title: Skapa en Azure-skalnings uppsättning som använder Tillgänglighetszoner
description: Lär dig hur du skapar skalnings uppsättningar för virtuella Azure-datorer som använder Tillgänglighetszoner för ökad redundans mot avbrott
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016666"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Skapa en skalnings uppsättning för virtuell dator som använder Tillgänglighetszoner

Du kan skapa en skalnings uppsättning mellan Tillgänglighetszoner för att skydda dina skalnings uppsättningar för virtuella datorer från data centers problem. Azure-regioner som har stöd för Tillgänglighetszoner har minst tre olika zoner, var och en med sin egen oberoende ström källa, nätverk och kylning. Mer information finns i [Översikt över Tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

När du distribuerar en regional (icke-zonindelade) skalnings uppsättning till en eller flera zoner från och med API version *2017-12-01* har du följande tillgänglighets alternativ:
- Maximal spridning (platformFaultDomainCount = 1)
- Statisk fast spridning (platformFaultDomainCount = 5)
- Spridningen är justerad med lagrings disk fel domäner (platforFaultDomainCount = 2 eller 3)

Med maximal spridning sprider skalnings uppsättningen dina virtuella datorer i så många fel domäner som möjligt inom varje zon. Spridningen kan vara över större eller mindre än fem fel domäner per zon. Med statisk fast spridning sprider skalnings uppsättningen dina virtuella datorer över exakt fem fel domäner per zon. Om skalnings uppsättningen inte kan hitta fem distinkta fel domäner per zon för att uppfylla tilldelnings förfrågan, Miss lyckas begäran.

**Vi rekommenderar att du distribuerar med maximal spridning för de flesta arbets belastningar**, eftersom den här metoden ger bästa möjliga spridning i de flesta fall. Om du behöver repliker som ska spridas mellan olika maskin varu isolerings enheter, rekommenderar vi att du sprider över Tillgänglighetszoner och använder maximal spridning i varje zon.

> [!NOTE]
> Med maximal spridning visas bara en feldomän i vyn skalnings uppsättning för virtuell dator instans och i instansens metadata, oavsett hur många fel domäner som de virtuella datorerna sprids över. Spridningen i varje zon är implicit.

### <a name="placement-groups"></a>Placeringsgrupper

När du distribuerar en skalnings uppsättning kan du också välja att distribuera med en enda [placerings grupp](./virtual-machine-scale-sets-placement-groups.md) per tillgänglighets zon, eller med flera per zon. För regionala (icke-zonindelade) skalnings uppsättningar är valet att ha en enda placerings grupp i regionen eller ha flera i regionen. För de flesta arbets belastningar rekommenderar vi flera placerings grupper som ger större skalning. I API version *2017-12-01* är skalnings inställningarna standardvärdet för flera placerings grupper för skalnings uppsättningar med en zon och flera zoner, men de är som standard en enda placerings grupp för regionala (icke-zonindelade) skalnings uppsättningar.

> [!NOTE]
> Om du använder maximal spridning måste du använda flera placerings grupper.

### <a name="zone-balancing"></a>Zon utjämning

Slutligen, för skalnings uppsättningar som distribueras över flera zoner, kan du också välja "bästa möjliga zon balans" eller "strikt zon balans". En skalnings uppsättning betraktas som "bal anse rad" om varje zon har samma antal virtuella datorer eller + \\ -1 virtuell dator i alla andra zoner för skalnings uppsättningen. Exempel:

- En skalnings uppsättning med 2 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 betraktas som balanserade. Det finns bara en zon med ett annat antal virtuella datorer och den är bara 1 mindre än de andra zonerna. 
- En skalnings uppsättning med 1 virtuell dator i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 betraktas som obalanser. Zon 1 har 2 färre virtuella datorer än zon 2 och 3.

Det är möjligt att virtuella datorer i skalnings uppsättningen har skapats, men tillägg på de virtuella datorerna inte kan distribueras. De här virtuella datorerna med anknytnings problem räknas fortfarande när du bestämmer om en skalnings uppsättning är bal anse rad. En skalnings uppsättning med 3 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 betraktas som balanserade även om alla tillägg misslyckades i zon 1 och alla tillägg lyckades i zon 2 och 3.

Med den bästa balansen i zonen kan skalnings uppsättningen försöka skala in och ut samtidigt som saldot upprätthålls. Men om det av någon anledning inte är möjligt (till exempel om en zon slutar fungera kan inte skalnings uppsättningen skapa en ny virtuell dator i den zonen), skalnings uppsättningen tillåter tillfällig obalans för att skala in eller ut. Vid efterföljande utskalning-försök lägger skalnings uppsättningen till virtuella datorer till zoner som behöver fler virtuella datorer för skalnings uppsättningen för att kunna bal anse ras. På samma sätt tar skalnings uppsättningen bort virtuella datorer från zoner som behöver färre virtuella datorer för skalnings uppsättningen för att kunna bal anse ras i efterföljande skala i försök. Med "strikt kreditlimit" Miss lyckas skalnings uppsättningen med försök att skala in eller ut om detta skulle orsaka obalanser.

Om du vill använda saldo för bästa möjliga zon anger du *zoneBalance* till *falskt*. Den här inställningen är standard i API-version *2017-12-01*. Om du vill använda strikt zon balans anger du *zoneBalance* till *Sant*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>En zon och en zon – redundanta skalnings uppsättningar

När du distribuerar en skalnings uppsättning för virtuella datorer kan du välja att använda en enda tillgänglighets zon i en region eller flera zoner.

När du skapar en skalnings uppsättning i en enda zon, styr du vilken zon alla de virtuella dator instanserna körs i, och skalnings uppsättningen hanteras och skalas bara inom den zonen. Med en zon – redundant skalnings uppsättning kan du skapa en enda skalnings uppsättning som sträcker sig över flera zoner. Eftersom virtuella dator instanser skapas, är de som standard jämnt balanserade över zoner. Om ett avbrott inträffar i en av zonerna skalar inte en skalnings uppsättning ut automatiskt för att öka kapaciteten. Vi rekommenderar att du konfigurerar regler för autoskalning baserat på processor-eller minnes användning. Reglerna för automatisk skalning tillåter skalnings uppsättningen att svara på förlust av virtuella dator instanser i en zon genom att skala ut nya instanser i de återstående drift zonerna.

Om du vill använda Tillgänglighetszoner måste din skalnings uppsättning skapas i en [Azure-region som stöds](../availability-zones/az-region.md). Du kan skapa en skalnings uppsättning som använder Tillgänglighetszoner med någon av följande metoder:

- [Azure-portalen](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Processen för att skapa en skalnings uppsättning som använder en tillgänglighets zon är densamma som beskrivs i [artikeln komma igång](quick-create-portal.md). När du väljer en Azure-region som stöds kan du skapa en skalnings uppsättning i en eller flera tillgängliga zoner, som du ser i följande exempel:

![Skapa en skalnings uppsättning i en enda tillgänglighets zon](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Skalnings uppsättningen och de stödda resurserna, till exempel Azure Load Balancer och offentlig IP-adress, skapas i den enskilda zon som du anger.

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Processen för att skapa en skalnings uppsättning som använder en tillgänglighets zon är densamma som beskrivs i [artikeln komma igång](quick-create-cli.md). Om du vill använda Tillgänglighetszoner måste du skapa din skalnings uppsättning i en Azure-region som stöds.

Lägg till `--zones` parametern i kommandot [AZ VMSS Create](/cli/azure/vmss) och ange vilken zon som ska användas (till exempel zon *1*, *2* eller *3*). I följande exempel skapas en skalnings uppsättning med en zon med namnet *myScaleSet* i zon *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Ett fullständigt exempel på en skalnings uppsättning för en zon och nätverks resurser finns i [det här EXEMPLET CLI-skript](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zon – redundant skalnings uppsättning

Om du vill skapa en zon – redundant skalnings uppsättning använder du en offentlig IP-adress och en belastningsutjämnare med *standard* -SKU. För förbättrad redundans skapar *standard* -SKU: n de zoner som är redundanta nätverks resurser. Mer information finns i [Azure Load Balancer standard – översikt](../load-balancer/load-balancer-overview.md) och [standard Load Balancer och Tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Om du vill skapa en zon – redundant skalnings uppsättning anger du flera zoner med `--zones` parametern. I följande exempel skapas en zon – redundant skalnings uppsättning med namnet *myScaleSet* över zon *1, 2, 3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Det tar några minuter att skapa och konfigurera alla skalnings uppsättnings resurser och virtuella datorer i den eller de zoner som du anger. Ett fullständigt exempel på en zon – redundant skalnings uppsättning och nätverks resurser finns i [det här EXEMPLET CLI-skript](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill använda Tillgänglighetszoner måste du skapa din skalnings uppsättning i en Azure-region som stöds. Lägg till `-Zone` parametern i kommandot [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) och ange vilken zon som ska användas (till exempel zon *1*, *2* eller *3*).

I följande exempel skapas en skalnings uppsättning med en zon med namnet *myScaleSet* i *östra USA 2* zon *1*. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och lastbalanserare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zon – redundant skalnings uppsättning

Om du vill skapa en zon – redundant skalnings uppsättning anger du flera zoner med `-Zone` parametern. I följande exempel skapas en zon – redundant skalnings uppsättning med namnet *myScaleSet* över *USA, östra 2* zon *1, 2, 3*. Den zon – redundanta Azure-nätverks resurser för virtuellt nätverk, offentlig IP-adress och belastningsutjämnare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Processen för att skapa en skalnings uppsättning som använder en tillgänglighets zon är densamma som beskrivs i komma igång-artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). Om du vill använda Tillgänglighetszoner måste du skapa din skalnings uppsättning i en Azure-region som stöds. Lägg till `zones` egenskapen till resurs typen *Microsoft. Compute/virtualMachineScaleSets* i mallen och ange vilken zon som ska användas (till exempel zon *1*, *2* eller *3*).

I följande exempel skapas en skalnings uppsättning med en enda zon med namnet *myScaleSet* i *USA, östra 2* zon *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Ett fullständigt exempel på en skalnings uppsättning för en zon och nätverks resurser finns i [den här exempel på en Resource Manager-mall](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zon – redundant skalnings uppsättning

Om du vill skapa en zon – redundant skalnings uppsättning anger du flera värden i `zones` egenskapen för resurs typen *Microsoft. Compute/virtualMachineScaleSets* . I följande exempel skapas en zon – redundant skalnings uppsättning med namnet *myScaleSet* över *USA, östra 2* zon *1, 2, 3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Om du skapar en offentlig IP-adress eller en belastningsutjämnare anger du *"SKU": {"name": "standard"} "* egenskapen för att skapa zoner som är redundanta nätverks resurser. Du måste också skapa en nätverks säkerhets grupp och regler för att tillåta all trafik. Mer information finns i [Azure Load Balancer standard – översikt](../load-balancer/load-balancer-overview.md) och [standard Load Balancer och Tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Ett fullständigt exempel på en zon – redundant skalnings uppsättning och nätverks resurser finns i [den här exempel på en Resource Manager-mall](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en skalnings uppsättning i en tillgänglighets zon kan du lära dig hur du [distribuerar program på virtuella datorers skalnings uppsättningar](tutorial-install-apps-cli.md) eller [använder autoskalning med skalnings uppsättningar för virtuella datorer](tutorial-autoscale-cli.md).
