---
title: Migrera virtuella datorer till Resource Manager med Azure CLI
description: Den här artikeln går igenom den plattforms oberoende migreringen av resurser från klassisk till Azure Resource Manager med hjälp av Azure CLI
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7537d4c19d6e196aeaa30f8c542e7b3e4e8702c1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505974"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrera IaaS-resurser från klassisk distribution till Azure Resource Manager med hjälp av Azure CLI

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](../classic-vm-deprecation.md#how-does-this-affect-me).

De här stegen visar hur du använder kommando rads kommandon i Azure (CLI) för att migrera infrastruktur som en tjänst (IaaS) resurser från den klassiska distributions modellen till Azure Resource Manager distributions modell. Artikeln kräver den [klassiska Azure CLI](/cli/azure/install-classic-cli). Eftersom Azure CLI bara är tillämpligt för Azure Resource Manager-resurser kan det inte användas för migreringen.

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har problem med en funktion som inte stöds eller ett konfigurations fel rekommenderar vi att du gör om åtgärden för att förbereda, avbryta eller bekräfta. Plattformen kommer sedan att försöka utföra åtgärden igen.
> 
> 

<br>
Här är ett flödes schema för att identifiera i vilken ordning stegen måste utföras under en migreringsprocessen

![Skärmbild som visar migreringsstegen](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Steg 1: Förbered för migrering
Här följer några tips som vi rekommenderar när du utvärderar migrering av IaaS-resurser från klassisk till Resource Manager:

* Läs igenom [listan med konfigurationer eller funktioner som inte stöds](../windows/migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder konfigurationer eller funktioner som inte stöds, rekommenderar vi att du väntar på att stöd för funktionen/konfigurationen ska tillkännages. Du kan också ta bort funktionen eller flytta bort från den konfigurationen för att aktivera migrering om den passar dina behov.
* Om du har automatiserade skript som distribuerar din infrastruktur och dina program idag kan du försöka skapa en liknande test installation genom att använda dessa skript för migrering. Du kan också konfigurera exempel miljöer med hjälp av Azure Portal.

> [!IMPORTANT]
> Programgatewayer stöds för närvarande inte för migrering från klassisk till Resource Manager. Om du vill migrera ett klassiskt virtuellt nätverk med en Application Gateway tar du bort gatewayen innan du kör en förberedelse åtgärd för att flytta nätverket. När du har slutfört migreringen ansluter du gatewayen i Azure Resource Manager. 
>
>ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration kan inte migreras automatiskt. I sådana fall tar du bort ExpressRoute-gatewayen, migrerar det virtuella nätverket och återskapar gatewayen. Mer information finns i [migrera ExpressRoute-kretsar och associerade virtuella nätverk från den klassiska distributions modellen till Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) .
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Steg 2: Ställ in din prenumeration och registrera providern
I scenarier med migrering måste du konfigurera din miljö för både klassisk och Resource Manager. [Installera Azure CLI](/cli/azure/install-classic-cli) och [Välj din prenumeration](/cli/azure/authenticate-azure-cli).

Logga in på ditt konto.

```azurecli
azure login
```

Välj Azure-prenumerationen med hjälp av följande kommando.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> Registreringen är en gång, men det måste göras en gång innan du försöker migrera. Utan att registrera dig visas följande fel meddelande 
> 
> *BadRequest: prenumerationen har inte registrerats för migrering.* 
> 
> 

Registrera hos resurs leverantören för migrering med hjälp av följande kommando. Observera att i vissa fall har det här kommandot nått sin tids gräns. Registreringen kommer dock att lyckas.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Vänta fem minuter tills registreringen är klar. Du kan kontrol lera statusen för godkännandet med hjälp av följande kommando. Kontrol lera att RegistrationState är `Registered` innan du fortsätter.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Växla nu CLI till `asm` läget.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 3: kontrol lera att du har tillräckligt med Azure Resource Manager virtuell dator virtuella processorer i Azure-regionen för din aktuella distribution eller VNET
För det här steget måste du växla till `arm` läge. Gör detta med följande kommando.

```azurecli
azure config mode arm
```

Du kan använda följande CLI-kommando för att kontrol lera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Mer information om vCPU kvoter finns i [gränser och Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

När du är klar med att verifiera det här steget kan du växla tillbaka till `asm` läget.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Steg 4: alternativ 1 – migrera virtuella datorer i en moln tjänst
Hämta listan över moln tjänster med hjälp av följande kommando och välj sedan den moln tjänst som du vill migrera. Observera att om de virtuella datorerna i moln tjänsten finns i ett virtuellt nätverk eller om de har webb-och arbets roller, får du ett fel meddelande.

```azurecli
azure service list
```

Kör följande kommando för att hämta distributions namnet för moln tjänsten från utförliga utdata. I de flesta fall är distributions namnet detsamma som namnet på moln tjänsten.

```azurecli
azure service show <serviceName> -vv
```

Verifiera först om du kan migrera moln tjänsten med följande kommandon:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Förbered de virtuella datorerna i moln tjänsten för migrering. Du kan välja mellan två alternativ.

Använd följande kommando om du vill migrera de virtuella datorerna till ett plattforms skapat virtuellt nätverk.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Använd följande kommando om du vill migrera till ett befintligt virtuellt nätverk i distributions modellen för Resource Manager.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

När förberedelse åtgärden har slutförts kan du titta igenom utförliga utdata för att hämta migreringen för de virtuella datorerna och se till att de är i rätt `Prepared` tillstånd.

```azurecli
azure vm show <vmName> -vv
```

Kontrol lera konfigurationen för de för beredda resurserna genom att använda antingen CLI eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Steg 4: alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk
Välj det virtuella nätverk som du vill migrera. Observera att om det virtuella nätverket innehåller webb-eller arbets roller eller virtuella datorer med konfigurationer som inte stöds, får du ett verifierings fel meddelande.

Hämta alla virtuella nätverk i prenumerationen med hjälp av följande kommando.

```azurecli
azure network vnet list
```

Utdata ser ut ungefär så här:

![Skärm bild av kommando raden med hela det virtuella nätverks namnet markerat.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

I exemplet ovan är **virtualNetworkName** hela namnet **"Group classicubuntu16 classicubuntu16"**.

Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Förbered det virtuella nätverket som du väljer för migrering med hjälp av följande kommando.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Kontrol lera konfigurationen för de för beredda virtuella datorerna med hjälp av CLI eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Steg 5: Migrera ett lagrings konto
När du har migrerat de virtuella datorerna rekommenderar vi att du migrerar lagrings kontot.

Förbered lagrings kontot för migrering med hjälp av följande kommando

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Kontrol lera konfigurationen för det för beredda lagrings kontot genom att använda CLI eller Azure Portal. Om du inte är redo för migrering och du vill gå tillbaka till det gamla läget använder du följande kommando.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Om den för beredda konfigurationen ser bra ut kan du flytta framåt och bekräfta resurserna med hjälp av följande kommando.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
