---
title: Aktivera värdbaserad kryptering på Azure Kubernetes Service (AKS)
description: Lär dig hur du konfigurerar en värdbaserad kryptering i ett Azure Kubernetes Service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772927"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Värdbaserad kryptering på Azure Kubernetes Service (AKS) (förhandsversion)

Med värdbaserad kryptering krypteras de data som lagras på VM-värden för dina AKS-agentnoder i vila och flöden krypteras till lagringstjänsten. Det innebär att temporära diskar krypteras i vila med plattformsbaserade nycklar. Cacheminnet för operativsystem och datadiskar krypteras i vila med antingen plattformsbaserade eller kund hanterade nycklar beroende på vilken krypteringstyp som angetts på diskarna. När du använder AKS, OS och datadiskar krypteras som standard vilolagrade med plattformsbaserade nycklar, vilket innebär att cacheminnen för dessa diskar också är krypterade i vila med plattforms hanterade nycklar som standard.  Du kan ange dina egna hanterade nycklar efter [BYOK (Bring Your Own Keys) med Azure-diskar i Azure Kubernetes Service](azure-disk-customer-managed-keys.md). Cacheminnet för dessa diskar krypteras sedan också med hjälp av den nyckel som du anger i det här steget.


## <a name="before-you-begin"></a>Innan du börjar

Den här funktionen kan bara anges när klustret skapas eller när nodpoolen skapas.

> [!NOTE]
> Värdbaserad kryptering är tillgängligt i [Azure-regioner][supported-regions] som stöder kryptering på serversidan av Azure-hanterade diskar och endast med specifika [VM-storlekar som stöds.][supported-sizes]

### <a name="prerequisites"></a>Förutsättningar

- Kontrollera att `aks-preview` CLI-tillägget v0.4.73 eller senare är installerat.
- Se till att du har `EnableEncryptionAtHostPreview` funktionsflaggan under `Microsoft.ContainerService` aktiverad.

Du måste aktivera funktionen för din prenumeration innan du använder egenskapen EncryptionAtHost för Azure Kubernetes Service klustret. Följ stegen nedan för att aktivera funktionen för din prenumeration:

1. Kör följande kommando för att registrera funktionen för din prenumeration

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. Kontrollera att registreringstillståndet är Registrerat (tar några minuter) med hjälp av kommandot nedan innan du provar funktionen.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster som är värdbaserad kryptering behöver du det senaste *CLI-tillägget aks-preview.* Installera *Azure CLI-tillägget aks-preview* med [kommandot az extension add][az-extension-add] eller sök efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

- Kan endast aktiveras för nya nodpooler.
- Kan endast aktiveras i [Azure-regioner som][supported-regions] stöder kryptering på serversidan av Azure-hanterade diskar och endast med specifika [VM-storlekar som stöds.][supported-sizes]
- Kräver ett AKS-kluster och en nodpool baserat på Virtual Machine Scale Sets(VMSS) som *VM-uppsättningstyp.*

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Använda värdbaserad kryptering på nya kluster (förhandsversion)

Konfigurera klusteragentnoderna så att de använder värdbaserad kryptering när klustret skapas. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Om du vill skapa kluster utan värdbaserad kryptering kan du göra det genom att utelämna `--enable-encryption-at-host` parametern .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Använda värdbaserad kryptering på befintliga kluster (förhandsversion)

Du kan aktivera värdbaserad kryptering på befintliga kluster genom att lägga till en ny nodpool i klustret. Konfigurera en ny nodpool att använda värdbaserad kryptering med hjälp av `--enable-encryption-at-host` parametern .

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Om du vill skapa nya nodpooler utan funktionen för värdbaserad kryptering kan du göra det genom att utelämna `--enable-encryption-at-host` parametern .

## <a name="next-steps"></a>Nästa steg

Läs [metodtips för AKS-klustersäkerhet][best-practices-security] Läs mer om [värdbaserad kryptering.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
