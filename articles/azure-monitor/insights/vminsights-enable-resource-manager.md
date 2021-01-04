---
title: Aktivera Azure Monitor for VMs med Resource Manager-mallar
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en eller flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med Azure PowerShell eller Azure Resource Manager mallar.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: cee7072f9bc844fb1f89168de3547dc726472b67
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695906"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Aktivera Azure Monitor for VMs med Resource Manager-mallar
I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en virtuell dator eller skalnings uppsättning för virtuella datorer med hjälp av Resource Manager-mallar. Den här proceduren kan användas för följande:

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Hybrid virtuell dator som är ansluten till Azure-bågen

## <a name="prerequisites"></a>Förutsättningar

- [Skapa och konfigurera en Log Analytics-arbetsyta](vminsights-configure-workspace.md). 
- Se [operativ system som stöds](vminsights-enable-overview.md#supported-operating-systems) för att säkerställa att operativ systemet för den virtuella datorn eller skalnings uppsättningen för virtuella datorer som du aktiverar stöds. 

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Vi har skapat exempel Azure Resource Manager mallar för att publicera dina virtuella datorer och skalnings uppsättningar för virtuella datorer. Dessa mallar innehåller scenarier som du kan använda för att aktivera övervakning av en befintlig resurs och skapa en ny resurs som har övervakning aktive rad.

>[!NOTE]
>Mallen måste distribueras i samma resurs grupp som den virtuella datorn eller skalnings uppsättningen för den virtuella datorn aktive ras.


Azure Resource Manager mallar finns i en arkivfil (. zip) som du kan [Ladda ned](https://aka.ms/VmInsightsARMTemplates) från vår GitHub-lagrings platsen. Innehållet i filen innehåller mappar som representerar varje distributions scenario med en mall och parameter fil. Innan du kör dem ändrar du parameter filen och anger de värden som krävs. 

Nedladdnings filen innehåller följande mallar för olika scenarier:

- **ExistingVmOnboarding** -mallen aktiverar Azure Monitor for VMS om den virtuella datorn redan finns.
- **NewVmOnboarding** -mallen skapar en virtuell dator och aktiverar Azure Monitor for VMS för att övervaka den.
- **ExistingVmssOnboarding** -mallen aktiverar Azure Monitor for VMS om den virtuella datorns skal uppsättning redan finns.
- **NewVmssOnboarding** -mallen skapar skalnings uppsättningar för virtuella datorer och aktiverar Azure Monitor for VMS för att övervaka dem.
- **ConfigureWorkspace** -mallen konfigurerar Log Analytics arbets ytan till att stödja Azure Monitor for VMS genom att aktivera lösningar och samling av prestanda räknare för Linux och Windows-operativsystem.

>[!NOTE]
>Om den virtuella datorns skalnings uppsättningar redan finns och uppgraderings principen är inställd på **manuell**, aktive ras Azure Monitor for VMS inte för instanser som standard när du har kört **ExistingVmssOnboarding** Azure Resource Manager-mallen. Du måste uppgradera instanserna manuellt.

## <a name="deploy-templates"></a>Distribuera mallar
Mallarna kan distribueras med [valfri distributions metod för Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md) , inklusive följande exempel med POWERSHELL och CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs.

- Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).

- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
