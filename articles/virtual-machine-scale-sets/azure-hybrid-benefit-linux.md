---
title: Azure Hybrid-förmån för skalnings uppsättningar för virtuella Linux-datorer
description: Lär dig hur Azure Hybrid-förmån kan använda skalnings uppsättningen för virtuella datorer för att hjälpa dig att spara pengar på dina virtuella Linux-datorer som körs på Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933686"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Azure Hybrid-förmån för skalnings uppsättning för virtuella Linux-datorer (offentlig för hands version)

**Azure Hybrid-förmån för skalnings uppsättningen för virtuella Linux-datorer finns nu i den offentliga för hands versionen**. AHB-förmånen kan hjälpa dig att minska kostnaderna för att köra dina RHEL och SLES för [virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Med den här förmånen betalar du bara för infrastruktur kostnaden för din skalnings uppsättning. Förmånen är tillgänglig för alla RHEL-och SLES Marketplace-avbildningar (PAYG).


>[!NOTE]
> I den här artikeln beskrivs Azure Hybrid-förmån för Linux-VMSS. Det finns en separat [-artikel som finns [här AHB för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), som redan är tillgänglig för Azure-kunder sedan november 2020.

## <a name="benefit-description"></a>Förmåns Beskrivning
Med Azure Hybrid kan du använda de befintliga moln åtkomst licenserna från Red Hat eller SUSE och flexibelt omvandla instanser för skalnings uppsättning för virtuella datorer för att hämta din BYOS-fakturering. 

Den skalnings uppsättning för virtuella datorer som distribueras från PAYG Marketplace-avbildningar debiterar både infrastruktur-och program varu avgiften. Med AHB kan PAYG-instanser konverteras till en BYOS fakturerings modell utan omdistribution.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Azure Hybrid-förmån kostnads visualisering på virtuella Linux-datorer.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Omfattning för Azure Hybrid-förmån berättigande för Linux
Azure Hybrid-förmån är tillgängligt för alla RHEL-och SLES-PAYG-avbildningar från Azure Marketplace. Förmånen är ännu inte tillgänglig för RHEL-eller SLES-BYOS-bilder eller anpassade avbildningar från Azure Marketplace.

Azures dedikerade värd instanser och SQL hybrid-förmåner är inte berättigade till Azure Hybrid-förmån om du redan använder fördelarna med virtuella Linux-datorer.

## <a name="get-started"></a>Kom igång

### <a name="red-hat-customers"></a>Red Hat-kunder

Azure Hybrid-förmån för RHEL är tillgänglig för Red Hat-kunder som uppfyller båda dessa kriterier:

- Ha aktiva eller oanvända RHEL-prenumerationer som är tillgängliga för användning i Azure
- Ha aktiverat en eller flera av dessa prenumerationer för användning i Azure med [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programmet

> [!IMPORTANT]
> Se till att rätt prenumeration har Aktiver ATS i [Cloud-Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programmet.

Börja använda fördelarna med Red Hat:

1. Aktivera dina berättigade RHEL-prenumerationer i Azure med hjälp av [Red Hat Cloud accesss kund gränssnitt](https://access.redhat.com/management/cloud).

   De Azure-prenumerationer som du anger under den Red Hat Cloud Access-aktiveringen kommer sedan att kunna använda Azure Hybrid-förmån funktionen.
1. Tillämpa Azure Hybrid-förmån på någon av dina befintliga och nya RHEL PAYG-skalnings uppsättningar för virtuella datorer. Du kan använda Azure Portal eller Azure CLI för att aktivera förmånen.
1. Följ rekommenderade [Nästa steg](https://access.redhat.com/articles/5419341) för att konfigurera uppdaterings källor för dina virtuella RHEL-datorer och för rikt linjer för RHEL-prenumerationer.


### <a name="suse-customers"></a>SUSE kunder

Börja använda fördelarna med SUSE:

1. Registrera dig för det offentliga moln programmet för SUSE.
1. Använd förmånen för din nyskapade eller befintliga skalnings uppsättning för virtuella datorer via Azure Portal eller Azure CLI.
1. Registrera dina virtuella datorer som får förmånen med en separat källa till uppdateringar.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Aktivera och inaktivera fördelen med Azure Portal 
Portal upplevelsen för att aktivera och inaktivera AHB på den virtuella datorns skalnings uppsättning är **inte tillgänglig för tillfället**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Aktivera och inaktivera förmånen med Azure CLI

Du kan använda `az vmss update` kommandot för att uppdatera befintliga virtuella datorer. Kör kommandot med en-parameter för virtuella RHEL-datorer `--license-type` `RHEL_BYOS` . Kör kommandot med en-parameter för virtuella SLES-datorer `--license-type` `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>CLI-exempel för att aktivera förmånen
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>CLI-exempel för att inaktivera förmånen
Om du vill inaktivera förmånen använder du `--license-type` värdet `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Skalnings uppsättningar har en ["uppgraderings princip"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) som avgör hur virtuella datorer hämtas med den senaste skalnings uppsättnings modellen. Om din VMSS har en automatisk uppgraderings princip kommer AHB-förmånen att tillämpas automatiskt när VM-instanser uppdateras. Om VMSS har en rullande uppgraderings princip, baserat på de schemalagda uppdateringarna, kommer AHB att tillämpas.
I händelse av en manuell uppgraderings princip måste du utföra "manuell uppgradering" för varje befintlig virtuell dator.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>CLI-exempel för att uppgradera instanser för skalnings uppsättning för virtuella datorer i händelse av en princip för manuell uppgradering 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Tillämpa Azure Hybrid-förmån på den virtuella datorns skalnings uppsättning skapa tid 
Förutom att använda Azure Hybrid-förmån i befintliga skalnings uppsättningar för virtuella datorer enligt principen betala per användning kan du anropa den när du skapar en virtuell dators skalnings uppsättning. Fördelarna med att göra detta är threefold:
- Du kan etablera både PAYG-och BYOS-instanser för skalnings uppsättning för virtuella datorer med hjälp av samma avbildning och process.
- Det möjliggör framtida ändringar i licens läge, något som inte är tillgängligt med en BYOS avbildning.
- Instanserna för skalnings uppsättningen för virtuella datorer ansluts till Red Hat-RHUI som standard för att säkerställa att de förblir uppdaterade och säkra. Du kan ändra den uppdaterade mekanismen efter distributionen när du vill.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>CLI-exempel för att skapa en skalnings uppsättning för virtuella datorer med AHB-förmån
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du skapar och uppdaterar virtuella datorer och lägger till licens typer (RHEL_BYOS, SLES_BYOS) för Azure Hybrid-förmån med hjälp av Azure CLI](/cli/azure/vmss)
