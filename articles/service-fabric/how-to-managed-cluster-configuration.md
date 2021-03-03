---
title: Konfigurera Service Fabric hanterat kluster (för hands version)
description: Lär dig hur du konfigurerar Service Fabric hanterat kluster för automatiska OS-uppgraderingar, NSG-regler och mycket annat.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732640"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Konfigurations alternativ för Service Fabric hanterat kluster (för hands version)

Förutom att välja det [Service Fabric hanterade kluster-SKU: n](overview-managed-cluster.md#service-fabric-managed-cluster-skus) när du skapar klustret, finns det ett antal andra sätt att konfigurera det. I den aktuella för hands versionen kan du:

* Konfigurera [nätverks alternativ](how-to-managed-cluster-networking.md) för klustret
* Lägg till ett [tillägg för skalnings uppsättning för virtuell dator](how-to-managed-cluster-vmss-extension.md) till en nodtyp
* Konfigurera [hanterad identitet](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) på olika nodtyper
* Aktivera [automatiska OS-uppgraderingar](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) för noderna
* Aktivera [kryptering av operativ system och data diskar](how-to-enable-managed-cluster-disk-encryption.md) på noderna

## <a name="enable-automatic-os-image-upgrades"></a>Aktivera automatiska uppgradering av operativ system avbildningar

Du kan välja att aktivera automatisk uppgradering av operativ system avbildningar till de virtuella datorerna som kör dina hanterade klusternoder. Även om de virtuella datorernas skalnings uppsättnings resurser hanteras för din räkning med Service Fabric hanterade kluster, är det du som väljer att aktivera automatiska uppgradering av operativ system avbildningar för dina klusternoder. Precis som med de [klassiska Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) -klustren uppgraderas inte hanterade klusternoder som standard för att förhindra oavsiktliga avbrott i klustret.

Så här aktiverar du automatiska OS-uppgraderingar:

* Använd `2021-01-01-preview` (eller senare) versionen av *Microsoft. ServiceFabric/Managedclusters* och *Microsoft. ServiceFabric/managedclusters/nodetypes* resurser
* Ange egenskapen `enableAutoOSUpgrade` för klustret till *Sant*
* Ange resurs egenskapen för kluster nodeTypes `vmImageVersion` till *senaste*

Exempel:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

När den är aktive rad börjar Service Fabric fråga och spåra operativ system avbildnings versioner i det hanterade klustret. Om det finns en ny operativ system version, uppgraderas klusternodernas typer (Virtual Machine Scale set), en i taget. Service Fabric körnings uppgraderingar utförs först efter det att du har bekräftat att uppgraderingar av OS-avbildningar för klusternoder pågår.

Om uppgraderingen Miss lyckas försöker Service Fabric igen efter 24 timmar, för högst tre återförsök. Precis som klassiska (ohanterade) Service Fabric-uppgraderingar kan felaktiga appar eller noder blockera uppgraderingen av operativ systemet.

Mer information om avbildnings uppgraderingar finns i [automatisk uppgradering av operativ system avbildningar med skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Nästa steg

[Översikt över Service Fabric hanterade kluster](overview-managed-cluster.md)

[Service Fabric-klustrets mallar](https://github.com/Azure-Samples/service-fabric-cluster-templates)
